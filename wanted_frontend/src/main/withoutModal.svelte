<script>
    import HeroBanner from "./heroBanner.svelte";
    import Tooltip from "./Tooltip.svelte"; 
    import { Progressbar } from 'flowbite-svelte';
    import { onMount } from 'svelte';

    let queryString = '';
    let scoreThreshold = 0.5;
    let frameInterval = 5;
    let apiUrl = "http://127.0.0.1:5000"; 

    let processing = false;
    let completed = false;
    let isNotReadyForSearch = false;
    let descriptions = [];
    let resultFileUrl = '';
    let previewImages = [];
    let currentIndex = 0;
    let sortBy = 'score';

    onMount(async () => {
        // loadMissingAlertMessage();
        // init();
    });
//5초 10초 20초 30초 단위, threshold 삭제
    async function loadMissingAlertMessage() {
        try {
            const response = await fetch(`${apiUrl}/fetch-data`);
            if (!response.ok) {
                throw new Error('Network response was not ok');
            }
            descriptions = await response.json();
            console.log('Fetched data:', descriptions);
        } catch (error) {
            console.error('Failed to fetch data:', error);
        }
    }

    async function handleAddQuery(queryString) {
        let url = `${apiUrl}/query`;
        if (queryString.length > 0) {
            const res = await fetch(url, {
                method: 'POST',
                headers: {
                    "Content-Type": "application/json",
                },
                body: JSON.stringify({ query: queryString }),
                mode: 'no-cors',
            });
            alert("탐색할 대상을 추가했습니다.");
        } else {
            alert("인상착의가 비었습니다.");
        }
    }

    async function handleStartSearch() {
        processing = true;
        completed = false;
        previewImages = [];
        currentIndex = 0;

        let url = `${apiUrl}/inference?scoreThreshold=${scoreThreshold}&frameInterval=${frameInterval}`;
        const response = await fetch(url);
        const blob = await response.blob();
        resultFileUrl = URL.createObjectURL(blob);

        processing = false;
        completed = true;

        await loadPreviewImages();
    }

    async function loadPreviewImages() {
        try {
            
            const response = await fetch(`${apiUrl}/high-probability-images`);
            if (!response.ok) {
                throw new Error('Failed to fetch preview images');
            }
            const imageList = await response.json();
            
            previewImages = imageList.map(img => {
                const originalFilename = img.filename;
                console.log(img.url);

                const [title, timeInSeconds, scoreRank] = parseFilename(originalFilename);                               
                return {
                    url: `${apiUrl}/images/${originalFilename}`,
                    filename: `${title} (${timeInSeconds})`,
                    location : img.url,
                    score_rank: scoreRank           
                };
            });

            previewImages.sort((a, b) => a.score_rank - b.score_rank);
        } catch (error) {
            console.error('Error fetching preview images:', error);
        }
    }

    function formatTime(seconds) {
        if (seconds >= 3600) {
            const hours = Math.floor(seconds / 3600);
            const minutes = Math.floor((seconds % 3600) / 60);
            const secs = seconds % 60;
            return `${hours}시간 ${minutes}분 ${secs}초`;
        } else if (seconds >= 60) {
            const minutes = Math.floor(seconds / 60);
            const secs = seconds % 60;
            return `${minutes}분 ${secs}초`;
        } else {
            return `${seconds}초`;
        }
    }

    function sortPreviewImages() {
        if (sortBy === 'score') {
            previewImages.sort((a, b) => a.score_rank - b.score_rank);
        } else if (sortBy === 'title') {
            previewImages.sort((a, b) => a.filename.localeCompare(b.filename));
        }
        previewImages = [...previewImages];
    }

    function parseFilename(filename) {
        // Extract video title and frame number from filename
        const regex = /^(.*?)의 (\d+)번째 프레임 결과(\d+)\.jpg$/;
        const match = filename.match(regex);

        if (match) {
            const title = match[1];
            const frameNumber = parseInt(match[2], 10);
            const scoreRank = parseInt(match[3], 10);
            const timeInSeconds = frameNumber * frameInterval;
            const formattedTime = formatTime(timeInSeconds);
            return [title, formattedTime, scoreRank];
        }

        // Fallback in case filename doesn't match the expected format
        return [filename, 0, 0];
    }
    
    const strAsset = {
        uploadVideo: "1. 실종자가 있다고 의심되는 cctv 영상을 업로드하세요.",
        enterQuery: "1. 실종자의 인상착의를 입력하세요",
        controlParameter: "2. Threshold와 Frame Interval을 설정하세요",
        startSearch: "찾기"
    };

</script>

<HeroBanner />
<div class="container {previewImages.length > 0 ? 'shifted' : 'centered'}">
    
    <div class="center-panel {previewImages.length > 0 ? 'center-panel-small' : 'center-panel-full'}" >
        {#if previewImages.length == 0}    
        <div class="descriptions">
            <h2>최근 실종 문자 내역</h2>
            {#if descriptions.length > 0}
                <table>
                    <thead>
                        <tr>
                            <th>지역</th>
                            <th>실종자 인상착의</th>
                            <th>문자 발생 일자</th>
                        </tr>
                    </thead>
                    <tbody>
                        {#each descriptions as { region, description, creation_date }}
                            <tr>
                                <td>{region}</td>
                                <td>{description.join(', ')}</td>
                                <td>{creation_date}</td>
                            </tr>
                        {/each}
                    </tbody>
                </table>
            {:else}
                <p>api 호출 중입니다.</p>
            {/if}
        </div>
        {/if}

        <div class="input-query">
            <p class="enterQuery">{strAsset.enterQuery}</p>
            <input id="queryInput" bind:value={queryString} placeholder="빨간 셔츠, 검정 바지" />
            <button class="queryButton" on:click={() => handleAddQuery(queryString)}>실종자 인상착의 제출</button>
        </div>

        <div class="controls">
            <p class="enterSetting">{strAsset.controlParameter}</p>
            <label for="scoreThreshold">Threshold 설정</label>
            <input id="scoreThreshold" type="range" min="0.1" max="1" step="0.05" bind:value={scoreThreshold} />
            <span>{scoreThreshold}</span>

            <label for="frameInterval">Frame Interval(초 단위)</label>
            <input id="frameInterval" type="range" min="3" max="6" step="1" bind:value={frameInterval} />
            <span>{frameInterval}  초 단위로 영상을 끊어 탐색합니다.</span>

            {#if processing}
                <button disabled>처리 중...</button>
            {:else if completed}
                <button class="searchButton" on:click={handleStartSearch}>{strAsset.startSearch}</button>
            {:else}
                <button id="searchStart" class="searchButton" disabled={isNotReadyForSearch} on:click={handleStartSearch}>{strAsset.startSearch}</button>
            {/if}
        </div>
    </div>

    {#if previewImages.length > 0 }        
    <div class="right-panel">
        <div class="image-preview">            
            <div class="header-container">
                <label class="radio-button">
                    <input 
                        type="radio" 
                        name="sort" 
                        value="score" 
                        checked={sortBy === 'score'}
                        on:change={() => { sortBy = 'score'; sortPreviewImages(); }}
                    />
                    <span class="custom-radio">정확도 순</span>
                </label>
                <label class="radio-button">
                    <input 
                        type="radio" 
                        name="sort" 
                        value="title" 
                        checked={sortBy === 'title'}
                        on:change={() => { sortBy = 'title'; sortPreviewImages(); }}
                    />
                    <span class="custom-radio">영상 제목 순</span>
                </label>
    
                {#if completed}
                    <div class="result-download">
                        <a href={resultFileUrl} download="result.zip">
                            <button>모든 결과 다운로드</button>
                        </a>
                    </div>
                {/if}
            </div>
            <div class="preview-list">
                {#each previewImages as image}
                    <div class="image-item">
                        <img src={image.url} alt="High probability frame" />
                        <a class="filename" href={image.url} target="_blank" rel="noopener noreferrer"> {image.filename}</a> <br> {image.location}                                                
                    </div>
                    
                {/each}
                <!-- {#each locationImages as meta}
                    <div class="image-item">
                        <p>{meta.filename}</p>
                        <p>{meta.location}</p>
                    </div>
                {/each} -->
            </div>
        </div>
    </div>
    {/if}
</div>

<style>
    :root {
        --primary-color: #2c3e50;
        --secondary-color: #1abc9c;
        --button-bg: #349e54;
        --button-bg-hover: #236838;
        --text-color: #2c3e50;
        --light-color: #ecf0f1;
        --font-family: 'Roboto', sans-serif;
    }

    tbody {
        font-family: var(--font-family);
    }

    .container {
        display: flex;
        justify-content: center;        
        margin: 0 auto;
        padding: 20px;
        transition: justify-content 300ms ease;
        max-width: 1200px;
    }

    .container.shifted {
        justify-content: space-between;
    }

    .center-panel {
        transition: width 0.3s ease;
        padding: 20px;
        background-color: var(--light-color);
        border-radius: 8px;
        box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);        
    }

    .center-panel-full {
        width: 70%;
    }

    .center-panel-small {
        width: 30%; 
    }

    .right-panel {
        width: 70%;
        display: flex;
        flex-direction: column;
        align-items: center;
        opacity: 1; 
        padding: 20px;
        background-color: var(--light-color);
        border-radius: 8px;
        box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    }

    .input-query {
        margin-top: 50px;
    }

    .controls {
        margin-top: 50px;
        margin-bottom: 20px;
    }

    .input-query input {
        width: 100%;
        padding: 15px;
        font-size: 16px;
        border: 2px solid var(--primary-color);
        border-radius: 8px;
        box-sizing: border-box;
        transition: border-color 0.3s;
    }

    .input-query input:focus {
        border-color: var(--secondary-color);
        outline: none;
    }

    .queryButton, .searchButton {
        padding: 12px 24px;
        font-size: 16px;
        background-color: var(--button-bg);
        color: var(--light-color);
        border: none;
        border-radius: 8px;
        transition: background-color 0.3s;
        margin-top: 20px;
    }

    .queryButton:hover, .searchButton:hover {
        background-color: var(--button-bg-hover);
    }

    .queryButton:disabled, .searchButton:disabled {
        background-color: #e0e0e0;
        cursor: not-allowed;
    }

    input[type="range"] {
        width: 100%;
        margin-top: 10px;
    }

    label {
        display: block;
        margin-top: 10px;
        font-size: 16px;
        color: var(--text-color);
    }

    span {
        display: block;
        margin-top: 5px;
    }

    .descriptions {
        margin-top: 30px;
        padding: 10px;
        background: #f4f4f4;
        border-radius: 5px;
        box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    }

    table {
        width: 100%;
        border-collapse: collapse;
    }

    table, th, td {
        border: 1px solid #ddd;
    }

    th, td {
        padding: 12px;
        text-align: left;
        font-size: 16px;
    }

    th {
        background-color: var(--primary-color);
        color: white;
    }

    td {
        color: var(--text-color);
    }

    .image-preview {
        margin-top: 20px;
    }

    .filename {
        margin-top: 10px;
        font-size: 14px;
    }

    .preview-list {
        display: flex;
        flex-direction: column;
        gap: 10px;
        align-items: center;
        width: 100%;
    }

    .image-item {
        display: flex;
        flex-direction: row; /* 이미지를 제목과 같은 줄에 배치 */
        align-items: center;
        width: 100%;
        padding: 10px;
        background-color: #f9f9f9;
        border-radius: 8px;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
        gap: 10px; /* 이미지와 파일명 사이의 간격 */
    }

    .image-item img {
        max-width: 200px;
        height: auto;
        border-radius: 8px;
    }

    .controls button[disabled] {
        margin-top: 20px;
    }

    .header-container {
    display: flex;
    align-items: center;
    justify-content: center;
    margin-bottom: 20px;
    }


    .result-download {
        margin-left: auto;
    }

    .result-download button {
        background-color: #364ebb;
        padding: 10px 20px;
        color: white;
        border-radius: 8px;
        font-size: 16px;
    }

    .result-download button:hover {
        cursor: pointer;
        background-color: #212a52;
    }

    .header-container label {
    margin-right: 10px;
    cursor: pointer;
    }

    .header-container input[type="radio"] {
        display: none; /* 기본 라디오 버튼 숨기기 */
    }

    .header-container span {
        padding: 10px 20px;
        border-radius: 8px;
        background-color: #e0e0e0; /* 기본 회색 배경 */
        color: #333;
        transition: background-color 0.3s, color 0.3s;
    }

    .header-container input[type="radio"]:checked + span {
        background-color: var(--primary-color); /* 선택된 버튼 파란색 */
        color: white; /* 선택된 버튼 글자색 흰색 */
    }

   
</style>