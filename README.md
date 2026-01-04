[index.html](https://github.com/user-attachments/files/24422451/index.html)
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Digital Showcase</title>
    <style>
        body { background: #000; color: #fff; font-family: sans-serif; margin: 0; padding: 15px; overflow: hidden; }
        .grid { display: grid; grid-template-columns: repeat(4, 1fr); grid-template-rows: repeat(3, 1fr); gap: 20px; height: 95vh; }
        .card { 
            background: #0a0a0a; border: 1px solid #222; border-radius: 12px; 
            display: flex; flex-direction: column; align-items: center; 
            padding: 15px; box-sizing: border-box; transition: all 0.8s ease-in-out; 
        }
        .card.active { transform: scale(1.06); border-color: #FFD700; background: #111; z-index: 10; }
        .img-container { width: 100%; height: 65%; background: #151515; border-radius: 8px; overflow: hidden; }
        .card img { width: 100%; height: 100%; object-fit: cover; }
        .title { color: #FFFFFF; font-size: 1.7vw; font-weight: bold; text-transform: uppercase; margin: 15px 0 5px 0; text-align: center; }
        .price { color: #FFD700; font-size: 2.4vw; font-weight: bold; }
    </style>
</head>
<body>
    <div id="board" class="grid">Загрузка данных...</div>
    <script>
        const url = "https://docs.google.com/spreadsheets/d/e/2PACX-1vQkJg-ateJ916_iAnG4ZUigWnVAh5Rhm0j7wutgrkf2Kl71EIckZiuwcZ4rvs8qEqTqfFEJHwaMdyZc/pub?output=csv";
        function clean(str) { return str ? str.replace(/["\r]/g, '').trim() : ""; }
        async function update() {
            try {
                const resp = await fetch(url + "&nocache=" + Date.now());
                const text = await resp.text();
                const rows = text.split('\n');
                let html = "";
                for (let i = 1; i <= 12; i++) {
                    if (rows[i]) {
                        const cols = rows[i].split(',');
                        const title = clean(cols[1]);
                        const price = clean(cols[2]);
                        const imgSrc = clean(cols[3]);
                        if (title) {
                            html += `<div class="card" id="card-${i}">
                                <div class="img-container"><img src="${imgSrc}" onerror="this.style.display='none';"></div>
                                <div class="title">${title}</div>
                                <div class="price">${price}</div>
                            </div>`;
                        } else { html += `<div class="card"></div>`; }
                    }
                }
                document.getElementById("board").innerHTML = html;
            } catch (e) { document.getElementById("board").innerHTML = "Ошибка связи"; }
        }
        let cur = 1;
        setInterval(() => {
            const cards = document.querySelectorAll('.card');
            cards.forEach(c => c.classList.remove('active'));
            const active = document.getElementById(`card-${cur}`);
            if (active) active.classList.add('active');
            cur = (cur % 12) + 1;
        }, 4000);
        update(); setInterval(update, 30000);
    </script>
</body>
</html>
