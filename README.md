<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>あや式 なんでも主人公4コマ化 - 完全版 -</title>
    <style>
        :root { 
            --bg-color: #0d0d0f;
            --card-bg: #16161a;
            --accent-gold: linear-gradient(135deg, #f3d078 0%, #d4af37 50%, #aa7c11 100%);
            --text-gold: #e5c158;
            --text-main: #f3f3f6;
            --border-color: #2a2a32;
        }
        body { font-family: 'Helvetica Neue', Arial, sans-serif; background: var(--bg-color); padding: 20px 15px; text-align: center; color: var(--text-main); }
        .card { background: var(--card-bg); border-radius: 24px; padding: 30px 25px; max-width: 420px; margin: auto; border: 1px solid var(--border-color); }
        h1 { background: var(--accent-gold); -webkit-background-clip: text; -webkit-text-fill-color: transparent; font-size: 1.5rem; margin-bottom: 5px; font-weight: 800; }
        .input-group { text-align: left; margin-bottom: 22px; }
        label { font-size: 0.8rem; font-weight: 700; color: var(--text-gold); display: flex; align-items: center; gap: 6px; margin-bottom: 8px; }
        input[type="text"], textarea { width: 100%; padding: 14px; background: #1f1f23; border: 1px solid var(--border-color); border-radius: 12px; box-sizing: border-box; font-size: 16px; color: var(--text-main); }
        .select-group { display: flex; gap: 6px; margin-top: 8px; justify-content: space-between; flex-wrap: wrap; }
        .select-tile-label { flex: 1; min-width: 80px; text-align: center; cursor: pointer; }
        .select-tile-label input[type="radio"] { opacity: 0; position: absolute; }
        .select-tile { padding: 10px 4px; border: 1px solid var(--border-color); border-radius: 10px; font-size: 0.7rem; font-weight: 700; background: #1f1f23; color: #a1a1aa; transition: 0.2s; }
        input[type="radio"]:checked + .select-tile { background: var(--accent-gold); border-color: transparent; color: #0d0d0f; }
        .btn-generate { background: var(--accent-gold); color: #0d0d0f; border: none; width: 100%; padding: 18px; border-radius: 30px; font-weight: 800; cursor: pointer; margin-top: 10px; }
        #result-area { display: none; margin-top: 30px; text-align: left; }
        .prompt-text { font-size: 0.85rem; line-height: 1.6; white-space: pre-wrap; color: #e4e4e7; background: #1f1f23; padding: 15px; border-radius: 14px; border: 1px solid var(--border-color); }
        .btn-copy { background: transparent; color: var(--text-gold); border: 1px solid #d4af37; width: 100%; padding: 14px; border-radius: 25px; font-weight: 700; cursor: pointer; margin-top: 12px; }
    </style>
</head>
<body>
<div class="card">
    <h1>CINEMATIC MANGA</h1>
    <div class="input-group">
        <label>① 主人公の特徴</label>
        <input type="text" id="userFeatures" placeholder="例：お団子ヘア、黒トップスの女性">
    </div>
    <div class="input-group">
        <label>② 召喚モード（言語）</label>
        <div class="select-group">
            <label class="select-tile-label"><input type="radio" name="mangaMode" value="ja" checked><div class="select-tile">🇯🇵 日本語</div></label>
            <label class="select-tile-label"><input type="radio" name="mangaMode" value="en"><div class="select-tile">🇺🇸 英語</div></label>
            <label class="select-tile-label"><input type="radio" name="mangaMode" value="canva"><div class="select-tile">🎨 文字なし</div></label>
        </div>
    </div>
    <div class="input-group">
        <label>③ 4コマ目のオチ</label>
        <div class="select-group">
            <label class="select-tile-label"><input type="radio" name="endingType" value="funny" checked><div class="select-tile">🍏 笑える</div></label>
            <label class="select-tile-label"><input type="radio" name="endingType" value="emotional"><div class="select-tile">🌸 感動</div></label>
            <label class="select-tile-label"><input type="radio" name="endingType" value="calm"><div class="select-tile">☕ 脱力</div></label>
            <label class="select-tile-label"><input type="radio" name="endingType" value="special"><div class="select-tile">💥 特殊</div></label>
        </div>
    </div>
    <div class="input-group">
        <label>④ 原作テキスト</label>
        <textarea id="noteContent" placeholder="文章をペースト"></textarea>
    </div>
    <button class="btn-generate" onclick="generateMangaPrompt()">最強プロンプトを召喚</button>
    <div id="result-area">
        <div id="promptText" class="prompt-text"></div>
        <button class="btn-copy" onclick="copyToClipboard()">📋 コピー</button>
    </div>
</div>
<script>
    function copyToClipboard() {
        navigator.clipboard.writeText(document.getElementById('promptText').innerText).then(() => alert("コピーしました✨"));
    }
    function generateMangaPrompt() {
        var features = document.getElementById('userFeatures').value;
        var text = document.getElementById('noteContent').value;
        var mode = document.querySelector('input[name="mangaMode"]:checked').value;
        var ending = document.querySelector('input[name="endingType"]:checked').value;
        
        var modeInstruction = "", strictRules = "";
        if (mode === "ja") {
            modeInstruction = "[DIALOGUE: JAPANESE ONLY] Natural Japanese speech bubbles.";
            strictRules = "[PROHIBITION: NO ENGLISH. No subtitles, no labels, no descriptions. Only Japanese text.]";
        } else if (mode === "en") {
            modeInstruction = "[DIALOGUE: ENGLISH ONLY] Punchy English dialogue.";
            strictRules = "[PROHIBITION: NO JAPANESE. Only English text.]";
        } else if (mode === "canva") {
            modeInstruction = "[NO TEXT] 100% clean artwork without any text or bubbles.";
            strictRules = "[PROHIBITION: NO TEXT. No Japanese, no English, no bubbles.]";
        }

        var endingInstruction = "";
        if (ending === "funny") endingInstruction = "PANEL 4: Most effective comedic ending for " + features + ". Focus on pose/expression.";
        else if (ending === "emotional") endingInstruction = "PANEL 4: Most touching resolution for " + features + ".";
        else if (ending === "calm") endingInstruction = "PANEL 4: Most cozy/relaxed conclusion. NO TEARS, NO WEEPING. Peaceful expression.";
        else if (ending === "special") endingInstruction = "PANEL 4: Dramatic/Shocking reaction for " + features + ".";

        var finalPrompt = "[ASPECT RATIO 9:32]\n" + strictRules + "\n\n" +
            "Style: Cinematic Manga. Character: " + features + "\n" +
            "[FACTUAL CONSTRAINT: Do not change the story facts. Keep background and context consistent.]\n\n" +
            "PANEL 1: Wide shot, establish context.\n" +
            "PANEL 2: Close-up, dramatic reaction.\n" +
            "PANEL 3: Medium shot, character action.\n" +
            endingInstruction + "\n" + modeInstruction + "\n\n" +
            "[STORY]:\n" + text;

        document.getElementById('promptText').innerText = finalPrompt;
        document.getElementById('result-area').style.display = "block";
    }
</script>
</body>
</html>
