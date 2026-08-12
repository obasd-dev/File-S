<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Telegram-Style Web Messenger</title>
    <style>
        :root {
            --bg-color: #0e1621;
            --sidebar-bg: #17212b;
            --chat-bg: #0e1621;
            --bubble-sent: #2b5278;
            --text-color: #f5f5f5;
            --text-muted: #7f91a4;
            --input-bg: #17212b;
            --accent-color: #5288c1;
            --hover-color: #202b36;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
        }

        body {
            background-color: var(--bg-color);
            color: var(--text-color);
            height: 100vh;
            display: flex;
            overflow: hidden;
        }

        /* Sidebar Layout */
        .sidebar {
            width: 320px;
            background-color: var(--sidebar-bg);
            border-right: 1px solid #101921;
            display: flex;
            flex-direction: column;
        }

        .sidebar-header {
            padding: 16px;
            font-size: 1.1rem;
            font-weight: 600;
            border-bottom: 1px solid #101921;
            color: var(--accent-color);
        }

        .chat-list-item {
            display: flex;
            align-items: center;
            padding: 12px 16px;
            cursor: pointer;
            background-color: var(--hover-color);
        }

        .avatar {
            width: 48px;
            height: 48px;
            border-radius: 50%;
            background-color: var(--accent-color);
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            margin-right: 12px;
        }

        /* Main Chat Area */
        .chat-container {
            flex: 1;
            display: flex;
            flex-direction: column;
            background-color: var(--chat-bg);
        }

        .chat-header {
            padding: 14px 20px;
            background-color: var(--sidebar-bg);
            border-bottom: 1px solid #101921;
            display: flex;
            align-items: center;
        }

        .chat-header .status {
            font-size: 0.8rem;
            color: var(--text-muted);
        }

        .messages-log {
            flex: 1;
            padding: 20px;
            overflow-y: auto;
            display: flex;
            flex-direction: column;
            gap: 10px;
        }

        /* Message Bubbles */
        .message-row {
            display: flex;
            justify-content: flex-end;
        }

        .message-bubble {
            max-width: 65%;
            background-color: var(--bubble-sent);
            padding: 10px 14px;
            border-radius: 12px 12px 0px 12px;
            display: flex;
            flex-direction: column;
            gap: 6px;
            word-wrap: break-word;
            box-shadow: 0 1px 2px rgba(0,0,0,0.3);
        }

        /* Message Formatting Preservation */
        .message-text {
            white-space: pre-wrap; /* Preserves tabs, spaces, and line breaks */
            word-break: break-word;
            font-size: 0.95rem;
            line-height: 1.4;
        }

        .message-media {
            max-width: 100%;
            border-radius: 8px;
            margin-top: 4px;
            /* Displays uncompressed image at native aspect ratio */
            object-fit: contain; 
        }

        .file-attachment {
            display: flex;
            align-items: center;
            background: rgba(0, 0, 0, 0.2);
            padding: 10px;
            border-radius: 8px;
            gap: 12px;
            text-decoration: none;
            color: var(--text-color);
        }

        .file-icon {
            width: 40px;
            height: 40px;
            background: var(--accent-color);
            border-radius: 6px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            font-size: 0.8rem;
            text-transform: uppercase;
        }

        .file-info {
            display: flex;
            flex-direction: column;
            overflow: hidden;
        }

        .file-name {
            font-size: 0.9rem;
            font-weight: 500;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }

        .file-size {
            font-size: 0.75rem;
            color: var(--text-muted);
        }

        .message-timestamp {
            font-size: 0.7rem;
            color: rgba(255, 255, 255, 0.6);
            align-self: flex-end;
            margin-top: 2px;
        }

        /* Input Bar */
        .input-bar {
            padding: 12px 20px;
            background-color: var(--sidebar-bg);
            display: flex;
            align-items: flex-end;
            gap: 12px;
            border-top: 1px solid #101921;
        }

        .attach-btn {
            background: none;
            border: none;
            cursor: pointer;
            color: var(--text-muted);
            padding: 8px;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: color 0.2s;
        }

        .attach-btn:hover {
            color: var(--text-color);
        }

        .message-input {
            flex: 1;
            background-color: var(--bg-color);
            border: 1px solid transparent;
            border-radius: 18px;
            padding: 10px 16px;
            color: var(--text-color);
            font-size: 0.95rem;
            outline: none;
            resize: none;
            max-height: 120px;
            min-height: 40px;
            line-height: 1.4;
        }

        .message-input:focus {
            border-color: var(--accent-color);
        }

        .send-btn {
            background-color: var(--accent-color);
            color: white;
            border: none;
            border-radius: 50%;
            width: 40px;
            height: 40px;
            cursor: pointer;
            display: flex;
            align-items: center;
            justify-content: center;
            transition: background-color 0.2s;
            flex-shrink: 0;
        }

        .send-btn:hover {
            background-color: #4272a3;
        }

        /* Preview overlay for attachments */
        .attachment-preview {
            display: none;
            padding: 8px 12px;
            background: var(--hover-color);
            border-radius: 8px;
            margin-bottom: 8px;
            font-size: 0.85rem;
            align-items: center;
            justify-content: space-between;
        }

        .attachment-preview.active {
            display: flex;
        }

        .remove-attachment {
            cursor: pointer;
            color: #ff5c5c;
            font-weight: bold;
            margin-left: 10px;
        }

        #fileInput {
            display: none;
        }
    </style>
</head>
<body>

    <!-- Sidebar -->
    <div class="sidebar">
        <div class="sidebar-header">Telegram Messenger</div>
        <div class="chat-list-item">
            <div class="avatar">TG</div>
            <div>
                <div style="font-weight: 600;">Saved Messages</div>
                <div style="font-size: 0.8rem; color: var(--text-muted);">Cloud Storage</div>
            </div>
        </div>
    </div>

    <!-- Chat View Container -->
    <div class="chat-container">
        <div class="chat-header">
            <div class="avatar" style="width:36px; height:36px; font-size:0.8rem;">TG</div>
            <div>
                <div style="font-weight: 600; font-size: 0.95rem;">Saved Messages</div>
                <div class="status">online</div>
            </div>
        </div>

        <!-- Chat Log -->
        <div class="messages-log" id="messagesLog"></div>

        <!-- Input Bar Area -->
        <div style="padding: 0 20px 12px 20px; background-color: var(--sidebar-bg);">
            <div class="attachment-preview" id="attachmentPreview">
                <span id="attachmentName">file.txt</span>
                <span class="remove-attachment" onclick="clearAttachment()">&#10005;</span>
            </div>
            
            <div class="input-bar" style="padding: 0; border: none;">
                <button class="attach-btn" onclick="document.getElementById('fileInput').click()" title="Attach File or Image">
                    <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                        <path d="M21.44 11.05l-9.19 9.19a6 6 0 0 1-8.49-8.49l9.19-9.19a4 4 0 0 1 5.66 5.66l-9.2 9.19a2 2 0 0 1-2.83-2.83l8.49-8.48"></path>
                    </svg>
                </button>
                <input type="file" id="fileInput" onchange="handleFileSelect(event)">
                
                <textarea 
                    id="messageInput" 
                    class="message-input" 
                    placeholder="Write a message..." 
                    rows="1" 
                    onkeydown="handleKeyDown(event)"></textarea>

                <button class="send-btn" onclick="sendMessage()">
                    <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
                        <line x1="22" y1="2" x2="11" y2="13"></line>
                        <polygon points="22 2 15 22 11 13 2 9 22 2"></polygon>
                    </svg>
                </button>
            </div>
        </div>
    </div>

    <script>
        let selectedFile = null;

        // Auto-resize input height based on content
        const messageInput = document.getElementById('messageInput');
        messageInput.addEventListener('input', function() {
            this.style.height = 'auto';
            this.style.height = (this.scrollHeight < 120 ? this.scrollHeight : 120) + 'px';
        });

        function handleKeyDown(event) {
            // Send on Enter (Shift+Enter for new line)
            if (event.key === 'Enter' && !event.shiftKey) {
                event.preventDefault();
                sendMessage();
            }
        }

        function handleFileSelect(event) {
            const file = event.target.files[0];
            if (!file) return;

            selectedFile = file;
            const previewBar = document.getElementById('attachmentPreview');
            const previewName = document.getElementById('attachmentName');
            
            previewName.textContent = `Attached: ${file.name} (${formatBytes(file.size)})`;
            previewBar.classList.add('active');
        }

        function clearAttachment() {
            selectedFile = null;
            document.getElementById('fileInput').value = '';
            document.getElementById('attachmentPreview').classList.remove('active');
        }

        function formatBytes(bytes) {
            if (bytes === 0) return '0 Bytes';
            const k = 1024;
            const sizes = ['Bytes', 'KB', 'MB', 'GB', 'TB'];
            const i = Math.floor(Math.log(bytes) / Math.log(k));
            return parseFloat((bytes / Math.pow(k, i)).toFixed(2)) + ' ' + sizes[i];
        }

        function escapeHTML(str) {
            return str.replace(/[&<>'"]/g, 
                tag => ({ '&': '&amp;', '<': '&lt;', '>': '&gt;', "'": '&#39;', '"': '&quot;' }[tag] || tag)
            );
        }

        function getCurrentTime() {
            const now = new Date();
            return now.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });
        }

        function sendMessage() {
            const text = messageInput.value;
            
            // Prevent sending if both input and attachment are empty
            if (!text.trim() && !selectedFile) return;

            const log = document.getElementById('messagesLog');
            
            const messageRow = document.createElement('div');
            messageRow.className = 'message-row';

            const bubble = document.createElement('div');
            bubble.className = 'message-bubble';

            // 1. Handle File/Image attachments without compression
            if (selectedFile) {
                // Generate a local Blob URL pointing directly to raw file data
                const fileUrl = URL.createObjectURL(selectedFile);

                if (selectedFile.type.startsWith('image/')) {
                    // Display uncompressed raw image file
                    const img = document.createElement('img');
                    img.src = fileUrl;
                    img.className = 'message-media';
                    img.alt = selectedFile.name;
                    bubble.appendChild(img);
                } else {
                    // Display downloadable large file card
                    const fileCard = document.createElement('a');
                    fileCard.href = fileUrl;
                    fileCard.download = selectedFile.name;
                    fileCard.className = 'file-attachment';
                    
                    const ext = selectedFile.name.split('.').pop() || 'file';
                    
                    fileCard.innerHTML = `
                        <div class="file-icon">${ext.substring(0, 4)}</div>
                        <div class="file-info">
                            <span class="file-name">${escapeHTML(selectedFile.name)}</span>
                            <span class="file-size">${formatBytes(selectedFile.size)}</span>
                        </div>
                    `;
                    bubble.appendChild(fileCard);
                }
            }

            // 2. Preserved Text Formatting
            if (text.trim()) {
                const textContainer = document.createElement('div');
                textContainer.className = 'message-text';
                // HTML-escaped to prevent XSS, rendered with preserved whitespace
                textContainer.innerHTML = escapeHTML(text);
                bubble.appendChild(textContainer);
            }

            // 3. Timestamp
            const timestamp = document.createElement('span');
            timestamp.className = 'message-timestamp';
            timestamp.textContent = getCurrentTime();
            bubble.appendChild(timestamp);

            messageRow.appendChild(bubble);
            log.appendChild(messageRow);

            // Auto scroll to bottom immediately
            log.scrollTop = log.scrollHeight;

            // Reset input controls
            messageInput.value = '';
            messageInput.style.height = 'auto';
            clearAttachment();
        }
    </script>
</body>
</html>
