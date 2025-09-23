<!DOCTYPE html>
<html lang="en-us">
<head>
    <meta charset="utf-8">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
    <title>Tuanjie WebGL Player | Web</title>
    <link rel="shortcut icon" href="TemplateData/favicon.ico">
    <link rel="stylesheet" href="TemplateData/style.css">
    <style>
        body, html { margin: 0; padding: 0; height: 100%; overflow: hidden; }

        /* Unity Canvas 容器 */
        #tuanjie-container { position: absolute; width: 100%; height: 100%; z-index: 1; }

        /* iframe 容器 */
        #external-iframe {
            display: none;
            position: absolute;
            top: 0; left: 0;
            width: 100%; height: 100%;
            border: none;
            z-index: 2; /* 可以覆盖 Unity Canvas */
        }
    </style>
</head>
<body>
    <!-- iframe 用于嵌入外部网页 -->
    <iframe id="external-iframe"></iframe>

    <!-- Unity Canvas 容器 -->
    <div id="tuanjie-container" class="tuanjie-desktop">
        <canvas id="tuanjie-canvas" width="960" height="600" tabindex="-1"></canvas>
        <div id="tuanjie-loading-bar">
            <div id="tuanjie-logo"></div>
            <div id="tuanjie-progress-bar-empty">
                <div id="tuanjie-progress-bar-full"></div>
            </div>
        </div>
        <div id="tuanjie-warning"> </div>
        <div id="tuanjie-footer">
            <div id="tuanjie-webgl-logo"></div>
            <div id="tuanjie-fullscreen-button"></div>
            <div id="tuanjie-build-title">Web</div>
        </div>
    </div>

    <script>
        var container = document.querySelector("#tuanjie-container");
        var canvas = document.querySelector("#tuanjie-canvas");
        var loadingBar = document.querySelector("#tuanjie-loading-bar");
        var progressBarFull = document.querySelector("#tuanjie-progress-bar-full");
        var fullscreenButton = document.querySelector("#tuanjie-fullscreen-button");
        var warningBanner = document.querySelector("#tuanjie-warning");

        function unityShowBanner(msg, type) {
            function updateBannerVisibility() {
                warningBanner.style.display = warningBanner.children.length ? 'block' : 'none';
            }
            var div = document.createElement('div');
            div.innerHTML = msg;
            warningBanner.appendChild(div);
            if (type == 'error') div.style = 'background: red; padding: 10px;';
            else {
                if (type == 'warning') div.style = 'background: yellow; padding: 10px;';
                setTimeout(function() {
                    warningBanner.removeChild(div);
                    updateBannerVisibility();
                }, 5000);
            }
            updateBannerVisibility();
        }

        var buildUrl = "Build";
        var loaderUrl = buildUrl + "/Web.loader.js";
        var config = {
            dataUrl: buildUrl + "/Web.data.gz",
            frameworkUrl: buildUrl + "/Web.framework.js.gz",
            codeUrl: buildUrl + "/Web.wasm.gz",
            streamingAssetsUrl: "StreamingAssets",
            companyName: "DefaultCompany",
            productName: "Web",
            productVersion: "0.1",
            showBanner: unityShowBanner,
        };

        if (/iPhone|iPad|iPod|Android/i.test(navigator.userAgent)) {
            var meta = document.createElement('meta');
            meta.name = 'viewport';
            meta.content = 'width=device-width, height=device-height, initial-scale=1.0, user-scalable=no, shrink-to-fit=yes';
            document.getElementsByTagName('head')[0].appendChild(meta);
            container.className = "tuanjie-mobile";
            canvas.className = "tuanjie-mobile";
        } else {
            canvas.style.width = "960px";
            canvas.style.height = "600px";
        }

        loadingBar.style.display = "block";

        var script = document.createElement("script");
        script.src = loaderUrl;
        script.onload = () => {
            createTuanjieInstance(canvas, config, (progress) => {
                progressBarFull.style.width = 100 * progress + "%";
            }).then((tuanjieInstance) => {
                loadingBar.style.display = "none";
                fullscreenButton.onclick = () => {
                    tuanjieInstance.SetFullscreen(1);
                };
            }).catch((message) => {
                alert(message);
            });
        };
        document.body.appendChild(script);

        // ---- iframe 控制函数 ----
        function ShowIframe(url) {
            var iframe = document.getElementById('external-iframe');
            iframe.src = url;
            iframe.style.display = 'block';
        }

        function HideIframe() {
            var iframe = document.getElementById('external-iframe');
            iframe.style.display = 'none';
            iframe.src = '';
        }

        // 页面加载完成后直接显示 iframe 示例
        window.addEventListener('load', () => {
            ShowIframe("https://www.baidu.com/"); // 替换为你需要嵌入的网页
        });

    </script>
</body>
</html>
