<script>
    import { onMount } from 'svelte';  
    import HeroBanner from '../main/heroBanner.svelte';  
    import Checkbox from '../general/Checkbox.svelte';
	import Radio from '../general/Radio.svelte';
	
    let group = 1;
	let selection = [];

    let files; 
    let apiUrl = "http://127.0.0.1:5000"; // Backend port: 5000
    let videoList = [];
    let error = '';
    let fileName = '';
    let message = '';
    let location = '';
    let video_select = '';
    onMount(async () => {
        handleGetVideoList();
    });

    async function handleGetVideoList(){
        try {
            let url = `${apiUrl}/video/list`;    
            const response = await fetch(url);
            const data = await response.json();

            if (response.ok && data.status_code === 200) {
                videoList = data.file_names;
            } else {
                error = data.description || 'Error fetching the video list';
            }
        } catch (err) {
            error = 'Error connecting to the server';
            console.error(err);
        }
    }

    async function deleteFile() {
        
        // if (!fileName) {
        //     message = 'Please enter a valid file name.';
        //     return;
        // }

        console.log(selection.length);
        
        if(selection.length != 1){
            alert ("please enter 1 file each delete");
        }else{
            fileName = selection[0];
            console.log(fileName);
            try {
                let url = `${apiUrl}/video/${fileName}`;            
                const response = await fetch(url, {
                    method: 'DELETE' ,               
                    body: fileName
            });

            const result = await response.text();

                if (response.ok) {
                    alert(`탐색할 비디오 목록에서 ${fileName}을 삭제했습니다.`);
                    handleGetVideoList();
                    message = result;
                } else {
                    message = `Error: ${result}`;
                }
            } catch (err) {
                message = 'Error connecting to the server';
                console.error(err);
            }

        }

        
    }

    function handleUpload() {             
        if (files && files.length > 0) {
            const formData = new FormData();
            formData.append('location', location); // Hardcoded location value
            formData.append('file', files[0]);

            let url = `${apiUrl}/video/file`;
            console.log(typeof(location));

            fetch(url, {
                method: 'POST',
                body: formData,
            })
            .then((response) => response.json())
            .then((result) => {
                handleGetVideoList();
                alert("탐색할 동영상이 업로드 되었습니다.");
                console.log('Success:', result);
            })
            .catch((error) => {
                console.error('Error:', error);
            });
        } else {
            alert("선택된 파일이 없습니다.");
            console.error('No file selected.');
        }
    }

    function buttonDisabled(){
        return (selection.length==0)
    }
 

    const strAsset = {
        uploadVideo: "실종자가 있다고 의심되는 cctv 영상을 업로드하세요.",        
    };

</script>

<HeroBanner/>
<div class="container">    
    <div class="upload-video">
        <p>{strAsset.uploadVideo}</p>
        <input id="fileUpload" type="file" bind:files class="file-input">        
        <p>영상의 장소를 입력하세요</p>
        <input id="locationUpload" type = "text" bind:value={location} class="location-input">        
        <button class="upload-btn" on:click={handleUpload}>CCTV 영상 업로드</button>
    </div>
    
    
    <h3>업로드된 비디오 목록</h3>
    <ul class="video-list">
        {#each videoList as video,i} 
            <li class="video-item">
                <label>
                    <input type="checkbox" bind:group={selection} value={video} />
                    {video}
                </label>
            </li>
        {/each}
    </ul>          
    
    <button class="delete-btn" on:click={deleteFile}>삭제</button>              

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
    .container {
        max-width: 600px;
        margin: 0 auto;
        padding: 20px;
        font-family: Arial, sans-serif;
    }

    .upload-video {
        background-color: #f9f9f9;
        padding: 20px;
        border-radius: 8px;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
        margin-bottom: 20px;
    }

    p {
        margin: 0 0 10px;
        font-weight: bold;
        color: #333;
    }

    .file-input {
        width: 100%;
        padding: 10px;
        margin: 10px 0;
        border-radius: 4px;
        border: 1px solid #ccc;
    }

    .upload-btn, .delete-btn {        
        background-color: var(--button-bg);
        color: white;
        border: none;
        padding: 10px 20px;
        text-align: center;
        display: inline-block;
        margin-top: 10px;
        font-size: 14px;
        border-radius: 4px;
        cursor: pointer;
        transition: background-color 0.3s;
    }

    .upload-btn:hover, .delete-btn:hover {
        background-color: var(--button-bg-hover);
    }

    h3 {
        color: #444;
        font-size: 18px;
        margin-bottom: 10px;
        text-align: left;
    }

    .video-list {
        list-style-type: none;
        padding: 0;
    }

    .video-item {
        background-color: #f0f0f0;
        margin-bottom: 8px;
        padding: 10px;
        border-radius: 4px;
        box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
        font-size: 14px;
        color: #333;
        text-align: left;
        transition: background-color 0.3s;
    }

    .video-item:hover {
        background-color: #e0e0e0;
    }

  

    label {
        font-weight: bold;
        color: #444;
        margin-bottom: 5px;
        display: block;
    }
</style>
