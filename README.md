<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ZEST</title>
    <style>
        body { 
            margin: 0; 
            background: #29825d; 
            font-family: Arial, sans-serif; 
        }
        #toolbar {
            background: #ffffff;
            padding: 12px;
            text-align: center;
            box-shadow: 0 4px 10px rgba(0,0,0,0.3);
            margin-bottom: 10px;
        }
        #toolbar label { 
            margin: 0 15px; 
            font-weight: bold; 
            color: #29825d;
        }
        #canvas { 
            background: white; 
            display: block; 
            margin: 0 auto; 
            box-shadow: 0 6px 15px rgba(0,0,0,0.4); 
            cursor: crosshair; 
            border: 3px solid #ffffff;
        }
        button { 
            padding: 8px 15px; 
            margin: 0 8px; 
            background: #29825d; 
            color: white; 
            border: none; 
            border-radius: 5px; 
            cursor: pointer; 
            font-weight: bold;
        }
        button:hover { 
            background: #1f6245; 
        }
        h1 {
            text-align: center;
            color: white;
            margin: 20px 0 10px;
            font-size: 2.5em;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.5);
        }
    </style>
</head>
<body>
    <h1>ZEST</h1>
    <div id="toolbar">
        <label>Color: <input type="color" id="colorPicker" value="#000000"></label>
        <label>Brush Size: <input type="range" id="brushSize" min="1" max="50" value="5"></label>
        <button id="clearBtn">Clear Canvas</button>
        <button id="eraserBtn">Eraser</button>
    </div>
    <canvas id="canvas" width="1000" height="700"></canvas>

    <script>
        const canvas = document.getElementById('canvas');
        const ctx = canvas.getContext('2d');
        const colorPicker = document.getElementById('colorPicker');
        const brushSize = document.getElementById('brushSize');
        const clearBtn = document.getElementById('clearBtn');
        const eraserBtn = document.getElementById('eraserBtn');

        let painting = false;
        let currentColor = colorPicker.value;

        function startPosition(e) {
            painting = true;
            draw(e);
        }

        function endPosition() {
            painting = false;
            ctx.beginPath();
        }

        function draw(e) {
            if (!painting) return;

            ctx.lineWidth = brushSize.value;
            ctx.lineCap = 'round';
            ctx.strokeStyle = currentColor;

            const rect = canvas.getBoundingClientRect();
            const x = e.clientX - rect.left;
            const y = e.clientY - rect.top;

            ctx.lineTo(x, y);
            ctx.stroke();
            ctx.beginPath();
            ctx.moveTo(x, y);
        }

        // Mouse events
        canvas.addEventListener('mousedown', startPosition);
        canvas.addEventListener('mouseup', endPosition);
        canvas.addEventListener('mousemove', draw);

        // Touch events for mobile support
        canvas.addEventListener('touchstart', (e) => {
            e.preventDefault();
            const touch = e.touches[0];
            const mouseEvent = new MouseEvent('mousedown', {
                clientX: touch.clientX,
                clientY: touch.clientY
            });
            canvas.dispatchEvent(mouseEvent);
        });
        canvas.addEventListener('touchend', (e) => {
            e.preventDefault();
            const mouseEvent = new MouseEvent('mouseup', {});
            canvas.dispatchEvent(mouseEvent);
        });
        canvas.addEventListener('touchmove', (e) => {
            e.preventDefault();
            const touch = e.touches[0];
            const mouseEvent = new MouseEvent('mousemove', {
                clientX: touch.clientX,
                clientY: touch.clientY
            });
            canvas.dispatchEvent(mouseEvent);
        });

        // Update color
        colorPicker.addEventListener('change', (e) => {
            currentColor = e.target.value;
        });

        // Eraser (switches to white)
        eraserBtn.addEventListener('click', () => {
            currentColor = '#ffffff';
            colorPicker.value = '#ffffff';
        });

        // Clear canvas
        clearBtn.addEventListener('click', () => {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            // Refill with white background
            ctx.fillStyle = 'white';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
        });

        // Initial white background
        ctx.fillStyle = 'white';
        ctx.fillRect(0, 0, canvas.width, canvas.height);
    </script>
</body>
</html>
