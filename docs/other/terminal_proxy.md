---
icon: lucide/network
---

# 终端代理

## 命令生成器

<div class="proxy-command-tool">
  <label for="proxy-port">代理端口</label>
  <input id="proxy-port" type="number" inputmode="numeric" min="1" max="65535" value="7890">

  <div class="proxy-platforms" aria-label="选择平台">
    <button type="button" class="active" data-platform="powershell">PowerShell</button>
    <button type="button" data-platform="cmd">CMD</button>
    <button type="button" data-platform="linux">Linux</button>
    <button type="button" data-platform="macos">macOS</button>
  </div>

  <div class="proxy-command-block">
    <div class="proxy-command-header">
      <span id="proxy-command-title">PowerShell</span>
      <button type="button" id="copy-proxy-command">复制</button>
    </div>
    <pre><code id="proxy-command"></code></pre>
  </div>
</div>

<script>
  (() => {
    const portInput = document.querySelector("#proxy-port");
    const platformButtons = document.querySelectorAll("[data-platform]");
    const commandTitle = document.querySelector("#proxy-command-title");
    const commandOutput = document.querySelector("#proxy-command");
    const copyButton = document.querySelector("#copy-proxy-command");
    let currentPlatform = "powershell";

    const platforms = {
      powershell: {
        label: "PowerShell",
        command: (port) => `$env:HTTP_PROXY="http://127.0.0.1:${port}"; $env:HTTPS_PROXY="http://127.0.0.1:${port}"; $env:ALL_PROXY="socks5://127.0.0.1:${port}"`
      },
      cmd: {
        label: "CMD",
        command: (port) => `set HTTP_PROXY=http://127.0.0.1:${port} && set HTTPS_PROXY=http://127.0.0.1:${port} && set ALL_PROXY=socks5://127.0.0.1:${port}`
      },
      linux: {
        label: "Linux",
        command: (port) => `export HTTP_PROXY=http://127.0.0.1:${port} HTTPS_PROXY=http://127.0.0.1:${port} ALL_PROXY=socks5://127.0.0.1:${port}`
      },
      macos: {
        label: "macOS",
        command: (port) => `export HTTP_PROXY=http://127.0.0.1:${port} HTTPS_PROXY=http://127.0.0.1:${port} ALL_PROXY=socks5://127.0.0.1:${port}`
      }
    };

    const getPort = () => {
      const port = Number.parseInt(portInput.value, 10);
      return Number.isInteger(port) && port > 0 && port <= 65535 ? port : "<PORT>";
    };

    const updateCommands = () => {
      const port = getPort();
      const platform = platforms[currentPlatform];
      commandTitle.textContent = platform.label;
      commandOutput.textContent = platform.command(port);
    };

    const copyText = async () => {
      if (navigator.clipboard && window.isSecureContext) {
        await navigator.clipboard.writeText(commandOutput.textContent);
      } else {
        const textarea = document.createElement("textarea");
        textarea.value = commandOutput.textContent;
        textarea.setAttribute("readonly", "");
        textarea.style.position = "fixed";
        textarea.style.opacity = "0";
        document.body.appendChild(textarea);
        textarea.select();
        document.execCommand("copy");
        textarea.remove();
      }
      const originalText = copyButton.textContent;
      copyButton.textContent = "已复制";
      window.setTimeout(() => {
        copyButton.textContent = originalText;
      }, 1200);
    };

    portInput.addEventListener("input", updateCommands);
    platformButtons.forEach((button) => {
      button.addEventListener("click", () => {
        currentPlatform = button.dataset.platform;
        platformButtons.forEach((item) => item.classList.toggle("active", item === button));
        updateCommands();
      });
    });
    copyButton.addEventListener("click", copyText);
    updateCommands();
  })();
</script>

## 配置代理

- Windows PowerShell

```powershell linenums="1"
$env:HTTP_PROXY="http://127.0.0.1:<PORT>"
$env:HTTPS_PROXY="http://127.0.0.1:<PORT>"
$env:ALL_PROXY="socks5://127.0.0.1:<PORT>"
```

- Windows CMD

```cmd linenums="1"
set HTTP_PROXY=http://127.0.0.1:<PORT>
set HTTPS_PROXY=http://127.0.0.1:<PORT>
set ALL_PROXY=socks5://127.0.0.1:<PORT>
```

- Linux / macOS

```sh linenums="1"
export HTTP_PROXY=http://127.0.0.1:<PORT>
HTTPS_PROXY=http://127.0.0.1:<PORT>
ALL_PROXY=socks5://127.0.0.1:<PORT>
```
