<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>SmartScan | Dashboard Demo</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/lucide/0.263.0/lucide.min.js"></script>
    <style>
        :root { --primary: #2563eb; --success: #16a34a; --danger: #dc2626; --bg: #f8fafc; }
        body { font-family: 'Segoe UI', sans-serif; margin: 0; background: #1e293b; display: flex; justify-content: center; align-items: center; height: 100vh; }
        .laptop-frame { width: 850px; background: white; border-radius: 12px; overflow: hidden; box-shadow: 0 25px 50px -12px rgba(0,0,0,0.5); display: flex; flex-direction: column; }
        header { background: var(--primary); color: white; padding: 1rem 1.5rem; display: flex; justify-content: space-between; align-items: center; }
        .main-layout { display: flex; height: 500px; }
        .camera-pane { flex: 1.4; background: #000; position: relative; display: flex; flex-direction: column; align-items: center; justify-content: center; color: white; }
        #video-feed { width: 100%; height: 100%; object-fit: cover; transform: scaleX(-1); display: none; }
        .info-pane { flex: 1; padding: 1.5rem; background: var(--bg); border-left: 1px solid #e2e8f0; overflow-y: auto; }
        .btn { border: none; padding: 0.8rem; border-radius: 8px; font-weight: bold; cursor: pointer; width: 100%; margin-bottom: 0.5rem; display: flex; align-items: center; justify-content: center; gap: 8px; }
        .btn-primary { background: var(--primary); color: white; }
        .btn-outline { background: transparent; border: 1px solid var(--primary); color: var(--primary); }
        .status-box { margin-top: 1rem; display: none; padding: 1rem; background: white; border-radius: 8px; border: 1px solid #e2e8f0; }
        .data-row { margin-bottom: 0.8rem; border-bottom: 1px solid #f1f5f9; padding-bottom: 0.3rem; }
        .label { font-size: 0.65rem; color: #64748b; font-weight: bold; text-transform: uppercase; display: block; }
        .value { font-size: 0.9rem; font-weight: 600; }
        .spinner { border: 3px solid #f3f3f3; border-top: 3px solid var(--primary); border-radius: 50%; width: 24px; height: 24px; animation: spin 1s linear infinite; display: none; margin: 10px auto; }
        @keyframes spin { 100% { transform: rotate(360deg); } }
    </style>
</head>
<body>

<div class="laptop-frame">
    <header>
        <div style="display: flex; align-items: center; gap: 10px;"><i data-lucide="activity"></i><span style="font-weight: bold;">SmartScan Industrial Dashboard</span></div>
        <span id="clock" style="font-family: monospace; font-size: 0.8rem;"></span>
    </header>

    <div class="main-layout">
        <div class="camera-pane" id="cam-container">
            <i data-lucide="camera-off" id="off-icon" size="48" style="opacity: 0.3; margin-bottom: 1rem;"></i>
            <video id="video-feed" autoplay playsinline></video>
            <div id="setup-msg" style="text-align: center; padding: 20px;">
                <p style="font-size: 0.9rem;">Camera Permission Required</p>
                <button class="btn btn-outline" onclick="initCamera()" style="color: white; border-color: white;">Enable Webcam</button>
            </div>
        </div>

        <div class="info-pane">
            <button class="btn btn-primary" onclick="analyze()">Run Automated Analysis</button>
            <label class="btn btn-outline" style="cursor: pointer;">
                <i data-lucide="upload"></i> Fallback: Upload Photo
                <input type="file" accept="image/*" style="display:none" onchange="handleUpload()">
            </label>

            <div id="loader" class="spinner"></div>

            <div id="results" class="status-box">
                <div id="tag" style="background: #dcfce7; color: #166534; padding: 0.4rem; border-radius: 4px; text-align: center; font-size: 0.75rem; font-weight: bold; margin-bottom: 1rem;">IDENTIFIED</div>
                <div class="data-row"><span class="label">Serial Number</span><span class="value" id="val-sn">SN-OX-2026-8841</span></div>
                <div class="data-row"><span class="label">Gas Content</span><span class="value">Oxygen (Industrial Grade)</span></div>
                <div class="data-row"><span class="label">Safety Status</span><span class="value" id="val-status" style="color: var(--success);">Compliant</span></div>
                <div class="data-row"><span class="label">Last Test Date</span><span class="value">2024-11-12</span></div>
                <div class="data-row"><span class="label">Next Test Due</span><span class="value">2026-11-12</span></div>
            </div>
        </div>
    </div>
</div>

<script>
    lucide.createIcons();
    setInterval(() => { document.getElementById('clock').textContent = new Date().toLocaleString(); }, 1000);

    const video = document.getElementById('video-feed');
    const setupMsg = document.getElementById('setup-msg');
    const offIcon = document.getElementById('off-icon');

    async function initCamera() {
        try {
            const stream = await navigator.mediaDevices.getUserMedia({ video: true });
            video.srcObject = stream;
            video.style.display = 'block';
            setupMsg.style.display = 'none';
            offIcon.style.display = 'none';
        } catch (err) {
            alert("Browser blocked camera. Please use the 'Upload Photo' fallback or check address bar permissions.");
        }
    }

    function analyze() {
        document.getElementById('results').style.display = 'none';
        document.getElementById('loader').style.display = 'block';
        setTimeout(() => {
            document.getElementById('loader').style.display = 'none';
            document.getElementById('results').style.display = 'block';
            // Randomize safety for demo
            const isSafe = Math.random() > 0.3;
            document.getElementById('val-status').textContent = isSafe ? "Compliant" : "TEST OVERDUE";
            document.getElementById('val-status').style.color = isSafe ? "var(--success)" : "var(--danger)";
            document.getElementById('tag').style.background = isSafe ? "#dcfce7" : "#fee2e2";
            document.getElementById('tag').style.color = isSafe ? "#166534" : "#991b1b";
        }, 1500);
    }

    function handleUpload() { analyze(); }
</script>
</body>
</html>
