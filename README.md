<title>Workspace Sandbox Client</title>
<style>
    html, body { margin: 0; padding: 0; width: 100%; height: 100%; overflow: hidden; background-color: #000; font-family: system-ui, sans-serif; }
    #display-frame { width: 100%; height: 100%; border: none; }
    #status-overlay { position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); color: #fff; text-align: center; }
</style>
<div id="status-overlay">Initializing Engine Components...</div>
<iframe id="display-frame" allow="autoplay; fullscreen; gamepad; keyboard; mouse;"></iframe>

<script>
    // Displays the server address at the very beginning of the execution
    alert("THIS IS THE SERVER IP, DONT ASK ME WHAT IT IS: wss://vchronos.share.zrok.io");

    // Utilizing a proxy/CDN stream to request the structural runtime files
    // This prevents the text editor from crashing due to large file sizes
    const targetUrl = "https://cdn.jsdelivr.net/gh/alexander-datskov/1.12-eaglercraftx@main/index.html";

    fetch(targetUrl)
        .then(response => {
            if (!response.ok) throw new Error("Network restriction encountered.");
            return response.text();
        })
        .then(html => {
            // Point the base reference to the repository directory to fetch scripts correctly
            const baseTag = `<base href="https://alexander-datskov.github.io/1.12-eaglercraftx/">`;
            const integratedHtml = html.replace("<head>", "<head>" + baseTag);
            
            // Mount the compiled engine structure directly into the frame memory space
            const frame = document.getElementById("display-frame");
            frame.srcdoc = integratedHtml;
            
            // Clear out loading messaging once structural transfer completes
            document.getElementById("status-overlay").style.display = "none";
        })
        .catch(err => {
            document.getElementById("status-overlay").innerHTML = 
                `<p style="color: #ff6b6b;">Execution Failed</p>` +
                `<p style="font-size: 14px;">The device administration policy or network firewall is restricting runtime injections.</p>`;
        });
</script>
