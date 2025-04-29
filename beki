<!DOCTYPE html><html lang="id">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>GitHub Manager</title>
  <style>
    body { background-color: #121212; color: #ffffff; font-family: 'Segoe UI', sans-serif; margin: 0; padding: 20px; }
    h1, p { text-align: center; }
    h1 { color: #00ff00; margin-bottom: 10px; }
    p { margin-bottom: 30px; color: #ccc; }
    .container { display: grid; grid-template-columns: repeat(auto-fit, minmax(350px, 1fr)); gap: 20px; max-width: 1200px; margin: auto; }
    .box { background: #1e1e1e; padding: 25px; border-radius: 12px; box-shadow: 0 0 15px rgba(0, 255, 196, 0.2); }
    label, input, select, textarea { display: block; width: 100%; margin-bottom: 10px; }
    input, select, textarea { padding: 10px; background: #2a2a2a; border: 1px solid #444; border-radius: 8px; color: white; }
    .btn { padding: 10px 15px; background: #00ffc4; color: #000; border: none; border-radius: 6px; cursor: pointer; margin: 5px 0; }
    .btn:hover { background: #00cfa7; }
    .loading { display: none; margin-top: 10px; color: #00ffc4; text-align: center; }
    #editBox { display: none; }
    .toast { position: fixed; top: 20px; right: 20px; padding: 15px; border-radius: 8px; color: white; font-weight: bold; z-index: 999; }
    .success { background: #28a745; }
    .error { background: #dc3545; }
    .preview { white-space: pre-wrap; background: #2a2a2a; padding: 10px; border-radius: 8px; margin-top: 10px; max-height: 300px; overflow: auto; display: none; }
  </style>
</head>
<body><h1>GitHub Manager</h1>
<p>Kelola file GitHub langsung dari browser</p><div class="container">
  <div class="box">
    <div class="form-group">
      <label for="token">GitHub Token</label>
      <input type="text" id="token" placeholder="Masukkan token GitHub">
      <button class="btn" style="margin-top:10px;" onclick="saveToken()">Simpan Token</button>
    </div>
    <label>Repo (username/repo)</label>
    <input type="text" id="repo" />
    <label>Branch</label>
    <input type="text" id="branch" value="main" />
    <label>Folder Tujuan</label>
    <input type="text" id="folderPath" placeholder="folder/subfolder" />
  </div>  <div class="box">
    <h2>File Manager</h2>
    <label>Pilih File</label>
    <select id="fileList">
      <option value="">-- Pilih file --</option>
    </select>
    <button class="btn" onclick="previewFile()">Preview</button>
    <div id="filePreview" class="preview"></div><label>Upload File</label>
<input type="file" id="fileUpload" multiple />
<button class="btn" onclick="listFiles()">List File</button>
<button class="btn" onclick="uploadFiles()">Upload</button>
<button class="btn" onclick="editFile()">Edit</button>
<button class="btn" onclick="deleteFile()">Hapus</button>
<button class="btn" onclick="renameFile()">Rename</button>

  </div>  <div class="box" id="editBox">
    <h2>Edit File</h2>
    <label>Path</label>
    <input type="text" id="filePath" readonly />
    <label>Isi File</label>
    <textarea id="fileContent" rows="10"></textarea>
    <button class="btn" onclick="saveFile()">Simpan</button>
    <button class="btn" onclick="cancelEdit()">Batal</button>
  </div>
</div><div class="loading" id="loading">Memproses...</div><script>
const GITHUB_API = "https://api.github.com";
const tokenInput = document.getElementById("token");

window.onload = () => {
  const savedToken = localStorage.getItem("github_token");
  if (savedToken) tokenInput.value = savedToken;
};

function getVal(id) {
  return document.getElementById(id).value.trim();
}

function showToast(msg, type = "success") {
  const el = document.createElement("div");
  el.className = `toast ${type}`;
  el.innerText = msg;
  document.body.appendChild(el);
  setTimeout(() => el.remove(), 3000);
}

function loading(state) {
  document.getElementById("loading").style.display = state ? "block" : "none";
}

function saveToken() {
  const token = tokenInput.value.trim();
  if (!token) return showToast("Token kosong!", "error");
  localStorage.setItem("github_token", token);
  showToast("Token disimpan!", "success");
}

async function listFiles() {
  const token = getVal("token"), repo = getVal("repo"), branch = getVal("branch"), path = getVal("folderPath");
  if (!token || !repo) return showToast("Token dan repo wajib diisi", "error");
  try {
    loading(true);
    const res = await fetch(`${GITHUB_API}/repos/${repo}/contents/${path}?ref=${branch}`, {
      headers: { Authorization: `token ${token}` }
    });
    const data = await res.json();
    const sel = document.getElementById("fileList");
    sel.innerHTML = `<option value="">-- Pilih file --</option>`;
    data.forEach(file => {
      if (file.type === "file") {
        const opt = document.createElement("option");
        opt.value = file.path;
        opt.text = file.name;
        sel.appendChild(opt);
      }
    });
    showToast("File dimuat");
  } catch (e) {
    showToast("Gagal: " + e.message, "error");
  } finally { loading(false); }
}

async function previewFile() {
  const path = getVal("fileList"), token = getVal("token"), repo = getVal("repo"), branch = getVal("branch");
  if (!path) return;
  try {
    const res = await fetch(`${GITHUB_API}/repos/${repo}/contents/${path}?ref=${branch}`, {
      headers: { Authorization: `token ${token}` }
    });
    const data = await res.json();
    const preview = atob(data.content.replace(/\n/g, ""));
    const previewEl = document.getElementById("filePreview");
    previewEl.innerText = preview;
    previewEl.style.display = "block";
  } catch {
    document.getElementById("filePreview").innerText = "Gagal memuat isi file";
  }
}

function cancelEdit() {
  document.getElementById("editBox").style.display = "none";
  document.getElementById("filePath").value = '';
  document.getElementById("fileContent").value = '';
}
</script></body>
</html>