<script>
    const API_KEY = "NeGo6JvVRY3BJZ2HUtYF";
    const MODEL_ID = "classification-waste/11";

    /** CAMERA VARIABLES **/
    let videoEl;
    let canvasEl;
    let videoStream = null;

    let photoData = null;      
    let cameraPrediction = null;
    let cameraLabel = null;    // <- final class only (camera)
    let loadingCamera = false;

    /** UPLOAD VARIABLES **/
    let uploadPhotoData = null;
    let uploadPrediction = null;
    let uploadLabel = null;    // <- final class only (upload)
    let loadingUpload = false;
    let uploadFile = null;

    /*********************************
     * 1. OPEN CAMERA
     *********************************/
    async function openCamera() {
        try {
            videoStream = await navigator.mediaDevices.getUserMedia({ video: true });
            videoEl.srcObject = videoStream;
            await videoEl.play();
        } catch (err) {
            alert("Camera blocked or unavailable");
        }
    }

    /*********************************
     * 2. TAKE PHOTO
     *********************************/
    let cameraFile = null;

    function takePhoto() {
        const ctx = canvasEl.getContext("2d");
        canvasEl.width = videoEl.videoWidth;
        canvasEl.height = videoEl.videoHeight;
        ctx.drawImage(videoEl, 0, 0);

        // show preview
        photoData = canvasEl.toDataURL("image/png");

        // convert to File
        canvasEl.toBlob((blob) => {
            cameraFile = new File([blob], "camera.png", { type: "image/png" });
        });
    }

    /*********************************
     * 3. UPLOAD PHOTO
     *********************************/
    function uploadPhoto(e) {
        uploadFile = e.target.files[0];
        uploadPhotoData = URL.createObjectURL(uploadFile);
    }

    /*********************************
     * 4. ROBOFLOW API
     *********************************/
    async function inferFile(file) {
        const formData = new FormData();
        formData.append("file", file);

        const url = `https://classify.roboflow.com/${MODEL_ID}?api_key=${API_KEY}`;

        const res = await fetch(url, {
            method: "POST",
            body: formData
        });

        return await res.json();
    }

    /*********************************
     * 5. CAMERA PREDICTION
     *********************************/
    async function predictCamera() {
        if (!cameraFile) return alert("Take a picture first!");

        loadingCamera = true;
        const result = await inferFile(cameraFile);
        loadingCamera = false;

        cameraPrediction = result;
        cameraLabel = result.predicted_classes?.[0] ?? "Unknown";
    }

    /*********************************
     * 6. UPLOAD PREDICTION
     *********************************/
    async function predictUpload() {
        if (!uploadFile) return alert("Upload a photo first!");

        loadingUpload = true;
        const result = await inferFile(uploadFile);
        loadingUpload = false;

        uploadPrediction = result;
        uploadLabel = result.predicted_classes?.[0] ?? "Unknown";
    }
</script>

<h1>Trash Classification (Roboflow)</h1>

<!-- CAMERA SECTION -->
<h2>Camera</h2>

<button class="btn" on:click={openCamera}>Open Camera</button>
<button class="btn" on:click={takePhoto}>Take Picture</button>

<video bind:this={videoEl} autoplay playsinline class="mirror" width="300"></video>
<canvas bind:this={canvasEl} style="display:none;"></canvas>

{#if photoData}
    <h3>Captured Photo:</h3>
    <img src={photoData} class="mirror" width="300">

    <button class="btn" on:click={predictCamera}>Predict</button>

    {#if loadingCamera}
        <p>Predicting…</p>
    {/if}

    {#if cameraLabel}
        <h3>Predicted Class: <span class="result">{cameraLabel}</span></h3>
    {/if}
{/if}

<hr />

<!-- UPLOAD SECTION -->
<h2>Upload Image</h2>

<input type="file" accept="image/*" on:change={uploadPhoto} />

{#if uploadPhotoData}
    <h3>Uploaded Photo:</h3>
    <img src={uploadPhotoData} width="300">

    <button class="btn" on:click={predictUpload}>Predict</button>

    {#if loadingUpload}
        <p>Predicting…</p>
    {/if}

    {#if uploadLabel}
        <h3>Predicted Class: <span class="result">{uploadLabel}</span></h3>
    {/if}
{/if}

<style>
    .btn {
        background: #333;
        color: white;
        padding: 10px 16px;
        border-radius: 8px;
        margin: 6px;
        cursor: pointer;
    }
    .mirror { transform: scaleX(-1); }
    .result {
        color: cyan;
        font-weight: bold;
        font-size: 1.2rem;
    }
</style>
