<!DOCTYPE html>
<html>
<head>
    <title>Workspace Sandbox Client</title>
    <style>
        html, body { margin: 0; padding: 0; width: 100%; height: 100%; overflow: hidden; background-color: #000; font-family: system-ui, sans-serif; }
        #display-frame { width: 100%; height: 100%; border: none; }
        #status-overlay { position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%); color: #fff; text-align: center; }
    </style>
</head>
<body>

    <div id="status-overlay">Initializing Engine Components...</div>
    <iframe id="display-frame" allow="autoplay; fullscreen; gamepad; keyboard; mouse;" sandbox="allow-scripts allow-same-origin"></iframe>

    <script>
        // Fires your IP/server popup immediately
        alert("COPY AND PASTE SERVER IP SO DONT ASK ME WHAT IT IS: wss://vchronos.shares.zrok.io");

        const targetUrl = "https://cdn.jsdelivr.net/gh/alexander-datskov/1.12-eaglercraftx@main/index.html";

        fetch(targetUrl)
            .then(response => {
                if (!response.ok) throw new Error("Network restriction encountered.");
                return response.text();
            })
            .then(html => {
                // Correctly routes Eaglercraft's scripts so they don't break
                const baseTag = `<base href="https://alexander-datskov.github.io/1.12-eaglercraftx/">`;
                const integratedHtml = html.replace("<head>", "<head>" + baseTag);
                
                // FIXED: Using Blob instead of Data URL so the iframe can inherit/use local storage
                const blob = new Blob([integratedHtml], { type: "text/html;charset=utf-8" });
                const blobUrl = URL.createObjectURL(blob);
                
                const frame = document.getElementById("display-frame");
                frame.src = blobUrl;
                
                document.getElementById("status-overlay").style.display = "none";
            })
            .catch(err => {
                // Friendly warning for when you are actively inside the W3Schools editor panel
                document.getElementById("status-overlay").innerHTML = 
                    `<p style="color: #ffaa00; font-weight: bold;">W3Schools Preview Blocked</p>` +
                    `<p style="font-size: 14px; color: #ccc; max-width: 400px; margin: 0 auto;">` +
                    `The alert box above worked! W3Schools blocks the game engine from loading inside its preview panel. ` +
                    `When users copy this code from your GitHub and save it as an <b>.html</b> file, it will work completely.</p>`;
            });
    </script>

</body>
</html>
