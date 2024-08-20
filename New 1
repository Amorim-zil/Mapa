<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mapa de Barra Mansa</title>
    <link rel="stylesheet" href="https://unpkg.com/leaflet/dist/leaflet.css" />
    <style>
        body {
            margin: 0;
            font-family: Arial, sans-serif;
        }
        #map {
            height: 100vh;
            width: 100%;
        }
        .calendar-overlay {
            position: fixed;
            top: 0;
            right: 0;
            width: 400px;
            height: 100vh;
            background: #ffffff;
            box-shadow: -2px 0 5px rgba(0, 0, 0, 0.1);
            overflow-y: auto;
            z-index: 1000;
            transform: translateX(100%);
            transition: transform 0.3s ease;
        }
        .calendar-overlay.open {
            transform: translateX(0);
        }
        .calendar-header {
            padding: 15px;
            background: #007bff;
            color: white;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        .calendar-header button {
            background: #ffffff;
            color: #007bff;
            border: none;
            border-radius: 5px;
            padding: 5px 10px;
            cursor: pointer;
        }
        .calendar-header button:hover {
            background: #e0e0e0;
        }
        .calendar-container {
            padding: 15px;
        }
        .calendar-year, .calendar-month {
            margin: 5px;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 5px;
            cursor: pointer;
            text-align: center;
            background: #f9f9f9;
        }
        .calendar-year:hover, .calendar-month:hover {
            background: #e0e0e0;
        }
        .calendar-days {
            display: grid;
            grid-template-columns: repeat(7, 1fr);
            gap: 5px;
            padding: 10px 0;
        }
        .calendar-day {
            padding: 10px;
            text-align: center;
            cursor: pointer;
            background: #f0f0f0;
            border-radius: 5px;
        }
        .calendar-day:hover {
            background-color: #d0d0d0;
        }
        .popup-scrollable {
            max-height: 200px;
            overflow-y: auto;
        }
        .go-to-rain, .go-home {
            position: fixed;
            bottom: 20px;
            background-color: #007bff;
            color: white;
            border: none;
            border-radius: 5px;
            padding: 10px 20px;
            cursor: pointer;
            font-size: 16px;
            z-index: 1000;
            transition: background-color 0.3s;
        }
        .go-to-rain {
            left: 20px;
        }
        .go-home {
            left: 150px;
        }
        .go-to-rain:hover, .go-home:hover {
            background-color: #0056b3;
        }
        .popup-content {
            text-align: center;
        }
        .leaflet-popup-content-wrapper {
            max-width: 250px;
        }
    </style>
</head>
<body>
    <div id="map"></div>
    <div id="calendarOverlay" class="calendar-overlay">
        <div class="calendar-header">
            <h2 id="calendarTitle">Calendário</h2>
            <button onclick="closeCalendar()">Fechar</button>
        </div>
        <div id="calendar" class="calendar-container"></div>
    </div>
    <button class="go-to-rain" onclick="goToRain()">Chuva</button>
    <button class="go-home" onclick="goHome()">Home</button>
    <script src="https://unpkg.com/leaflet/dist/leaflet.js"></script>
    <script>
         // Coordenadas centrais para Barra Mansa, RJ
         const lat = -22.541112;
        const lon = -44.177198;

        // Coordenadas do ícone de chuva
        const rainLat = -22.3036;
        const rainLon = -44.1869;

        // Dados de chuva para todos os anos e meses
        const rainData = {
    
            "2000-01": 305.2, "2000-02": 289.5, "2000-03": 161.8, "2000-04": 20.5, "2000-05": 7.3,
    "2000-06": 0.2, "2000-07": 67.4, "2000-08": 53.5, "2000-09": 189.8, "2000-10": 70.2,
    "2000-11": 300.8, "2000-12": 303.0,
    "2001-01": 145.0, "2001-02": 266.5, "2001-03": 228.8, "2001-04": 15.5, "2001-05": 57.3,
    "2001-06": 5.0, "2001-07": 14.0, "2001-08": 63.9, "2001-09": 37.3, "2001-10": 110.5,
    "2001-11": 171.9, "2001-12": 250.6,
    "2002-01": 313.1, "2002-02": 295.9, "2002-03": 237.3, "2002-04": 50.7, "2002-05": 29.3,
    "2002-06": 4.2, "2002-07": 39.4, "2002-08": 40.1, "2002-09": 71.3, "2002-10": 142.5,
    "2002-11": 200.8, "2002-12": 307.6,
    "2003-01": 457.1, "2003-02": 75.8, "2003-03": 347.2, "2003-04": 43.3, "2003-05": 19.8,
    "2003-06": 14.9, "2003-07": 18.5, "2003-08": 40.0, "2003-09": 44.6, "2003-10": 154.5,
    "2003-11": 217.3, "2003-12": 170.3,
    "2004-01": 208.8, "2004-02": 470.1, "2004-03": 224.3, "2004-04": 295.9, "2004-05": 79.5,
    "2004-06": 28.5, "2004-07": 57.7, "2004-08": 5.9, "2004-09": 10.5, "2004-10": 211.8,
    "2004-11": 219.8, "2004-12": 322.5,
    "2005-01": 396.1, "2005-02": 176.3, "2005-03": 224.0, "2005-04": 250.6, "2005-05": 62.8,
    "2005-06": 12.7, "2005-07": 48.8, "2005-08": 3.9, "2005-09": 68.4, "2005-10": 42.3,
    "2005-11": 233.0, "2005-12": 315.7,
    "2006-01": 201.8, "2006-02": 505.9, "2006-03": 87.0, "2006-04": 70.6, "2006-05": 46.5,
    "2006-06": 10.0, "2006-07": 12.7, "2006-08": 20.3, "2006-09": 47.5, "2006-10": 124.5,
    "2006-11": 220.5, "2006-12": 242.7,
    "2007-01": 378.1, "2007-02": 157.4, "2007-03": 25.3, "2007-04": 98.4, "2007-05": 58.5,
    "2007-06": 5.8, "2007-07": 37.1, "2007-08": 4.0, "2007-09": 27.5, "2007-10": 69.5,
    "2007-11": 262.2, "2007-12": 197.8,
    "2008-01": 273.8, "2008-02": 419.9, "2008-03": 417.3, "2008-04": 148.0, "2008-05": 23.3,
    "2008-06": 25.5, "2008-07": 1.3, "2008-08": 73.3, "2008-09": 64.7, "2008-10": 143.1,
    "2008-11": 310.2, "2008-12": 364.0,
    "2009-01": 377.4, "2009-02": 546.6, "2009-03": 191.3, "2009-04": 43.5, "2009-05": 21.7,
    "2009-06": 31.8, "2009-07": 21.3, "2009-08": 44.2, "2009-09": null, "2009-10": 241.5,
    "2009-11": 451.8, "2009-12": 453.2,
    "2010-01": 336.1, "2010-02": 192.7, "2010-03": 284.2, "2010-04": 170.8, "2010-05": 33.7,
    "2010-06": 6.1, "2010-07": 69.3, "2010-08": 2.0, "2010-09": 56.6, "2010-10": 155.8,
    "2010-11": 321.7, "2010-12": 579.3,
    "2011-01": 264.5, "2011-02": 145.0, "2011-03": 262.4, "2011-04": 84.2, "2011-05": 16.0,
    "2011-06": 30.2, "2011-07": 3.9, "2011-08": 13.4, "2011-09": 0.0, "2011-10": 218.3,
    "2011-11": 184.2, "2011-12": 176.0,
    "2012-01": 346.1, "2012-02": 193.6, "2012-03": 123.0, "2012-04": 36.2, "2012-05": 18.7,
    "2012-06": 52.6, "2012-07": 37.3, "2012-08": 48.2, "2012-09": 26.8, "2012-10": 216.2,
    "2012-11": 172.0, "2012-12": 253.5,
    "2013-01": 243.1, "2013-02": 148.3, "2013-03": 239.0, "2013-04": 56.1, "2013-05": 20.5,
    "2013-06": 33.3, "2013-07": 25.4, "2013-08": 0.0, "2013-09": 26.5, "2013-10": 85.1,
    "2013-11": 245.2, "2013-12": 211.7,
    "2014-01": 411.0, "2014-02": 294.8, "2014-03": 264.1, "2014-04": 96.5, "2014-05": 32.5,
    "2014-06": 6.1, "2014-07": 38.0, "2014-08": 15.3, "2014-09": 111.4, "2014-10": 86.7,
    "2014-11": 239.2, "2014-12": 116.1,
    "2015-01": 118.5, "2015-02": 74.7, "2015-03": 245.2, "2015-04": 119.8, "2015-05": 14.8,
    "2015-06": 0.0, "2015-07": 5.7, "2015-08": 3.3, "2015-09": 22.0, "2015-10": 239.6,
    "2015-11": 228.7, "2015-12": 270.2,
    "2016-01": 123.7, "2016-02": 174.4, "2016-03": 183.5, "2016-04": 106.8, "2016-05": 105.5,
    "2016-06": 11.4, "2016-07": 40.2, "2016-08": 37.3, "2016-09": 54.7, "2016-10": 78.8,
    "2016-11": 185.2, "2016-12": 193.0,
    "2017-01": 404.8, "2017-02": 96.3, "2017-03": 168.2, "2017-04": 98.0, "2017-05": 47.8,
    "2017-06": 49.9, "2017-07": 6.7, "2017-08": 63.0, "2017-09": 17.5, "2017-10": 91.5,
    "2017-11": 156.1, "2017-12": 177.9,
    "2018-01": 337.3, "2018-02": 212.4, "2018-03": 287.4, "2018-04": 123.4, "2018-05": 31.5,
    "2018-06": 16.2, "2018-07": 8.3, "2018-08": 62.6, "2018-09": 139.8, "2018-10": 112.6,
    "2018-11": 209.3, "2018-12": 257.6,
    "2019-01": 234.1, "2019-02": 224.5, "2019-03": 253.3, "2019-04": 110.7, "2019-05": 148.5,
    "2019-06": 21.0, "2019-07": 13.9, "2019-08": 20.7, "2019-09": 70.5, "2019-10": 126.7,
    "2019-11": 151.0, "2019-12": 234.0,
    "2020-01": 265.4, "2020-02": 483.2, "2020-03": 357.5, "2020-04": 4.1, "2020-05": 45.4,
    "2020-06": 13.1, "2020-07": 23.9, "2020-08": 35.4, "2020-09": 85.0, "2020-10": 54.1,
    "2020-11": 233.3, "2020-12": 466.8,
    "2021-01": 198.5, "2021-02": 288.8, "2021-03": 121.8, "2021-04": 25.3, "2021-05": 61.2,
    "2021-06": 16.1, "2021-07": 4.9, "2021-08": 93.0, "2021-09": 41.3, "2021-10": 196.3,
    "2021-11": 204.9, "2021-12": 201.6,
    "2022-01": 320.4, "2022-02": 332.4, "2022-03": 335.0, "2022-04": 58.1, "2022-05": 1.7,
    "2022-06": 14.4, "2022-07": 6.9, "2022-08": 8.4, "2022-09": 127.5, "2022-10": 54.2,
    "2022-11": 448.5, "2022-12": 327.0,
    "2023-01": 369.7, "2023-02": 410.6, "2023-03": 253.0, "2023-04": 127.3, "2023-05": 23.1,
    "2023-06": 10.5, "2023-07": 36.1, "2023-08": 39.3, "2023-09": 20.5, "2023-10": 221.2,
    "2023-11": 222.3, "2023-12": 222.3
}


        // Inicializa o mapa
        const map = L.map('map').setView([lat, lon], 13);

        // Adiciona a camada de tiles do OpenStreetMap
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
        }).addTo(map);

        // Adiciona um ícone personalizado para Barra Mansa
        const barraMansaIcon = L.icon({
            iconUrl: 'https://img.icons8.com/ios/50/000000/marker.png',
            iconSize: [40, 40],
            iconAnchor: [20, 40],
            popupAnchor: [0, -40]
        });

        // Adiciona o marcador para Barra Mansa
        L.marker([lat, lon], { icon: barraMansaIcon }).addTo(map)
            .bindPopup('Barra Mansa, RJ')
            .openPopup();

        // Adiciona um ícone personalizado para chuva
        const rainIcon = L.icon({
            iconUrl: 'https://img.icons8.com/ios/50/000000/rain.png',
            iconSize: [32, 32],
            iconAnchor: [16, 32],
            popupAnchor: [0, -32]
        });

        // Adiciona o marcador de chuva com popup
        const rainMarker = L.marker([rainLat, rainLon], { icon: rainIcon }).addTo(map)
            .bindPopup(`
                <div class="popup-content">
                    <h3>Informações sobre a Chuva</h3>
                    <div class="popup-scrollable">
                        <p><strong>Nome:</strong> RIBEIRÃO DE SÃO JOAQUIM</p>
                        <p><strong>Responsável Sigla:</strong> ANA</p>
                        <p><strong>Operadora:</strong> Serviço Geológico do Brasil</p>
                        <p><strong>Operadora Sigla:</strong> SGB-CPRM</p>
                        <p><strong>Operadora Unidade:</strong> SP</p>
                        <p><strong>Roteiro:</strong> 2</p>
                        <p><strong>Tipo:</strong> Pluviométrica</p>
                        <p><strong>Operando:</strong> Sim</p>
                        <div class="popup-scrollable">
                            <button class="show-calendar" onclick="showCalendar()">Ver Calendário</button>
                        </div>
                    </div>
                </div>
            `)
            .on('popupopen', () => {
                console.log('Popup aberto');
            });

        // Função para calcular a soma da chuva em um ano
        function getYearlyRainTotal(year) {
            return Object.keys(rainData)
                .filter(date => date.startsWith(year))
                .reduce((total, date) => total + (rainData[date] || 0), 0);
        }

        // Função para calcular a soma da chuva em um mês
        function getMonthlyRainTotal(year, month) {
            return Object.keys(rainData)
                .filter(date => date.startsWith(`${year}-${String(month + 1).padStart(2, '0')}`))
                .reduce((total, date) => total + (rainData[date] || 0), 0);
        }

        // Função para mostrar o calendário
        function showCalendar() {
            const overlay = document.getElementById('calendarOverlay');
            overlay.classList.add('open');
            showYears();
        }

        // Função para fechar a aba do calendário
        function closeCalendar() {
            const overlay = document.getElementById('calendarOverlay');
            overlay.classList.remove('open');
        }

        // Função para mostrar os anos no calendário
        function showYears() {
            const calendar = document.getElementById('calendar');
            const startYear = 2000;
            const endYear = 2024;
            let html = '<div class="calendar-years">';
            for (let year = startYear; year <= endYear; year++) {
                const totalRain = getYearlyRainTotal(year);
                html += `
                    <div class="calendar-year" onclick="showMonths(${year})">
                        ${year} <br> ${totalRain.toFixed(1)} mm
                    </div>`;
            }
            html += '</div>';
            calendar.innerHTML = html;
            document.getElementById('calendarTitle').textContent = 'Selecione um Ano';
        }

        // Função para mostrar os meses ao clicar em um ano
        function showMonths(year) {
            const calendar = document.getElementById('calendar');
            let html = '<div class="calendar-months">';
            for (let month = 0; month < 12; month++) {
                const totalRain = getMonthlyRainTotal(year, month);
                const monthName = new Date(year, month).toLocaleString('pt-BR', { month: 'long' });
                html += `
                    <div class="calendar-month" onclick="showDays(${year}, ${month + 1})">
                        ${monthName} <br> ${totalRain.toFixed(1)} mm
                    </div>`;
            }
            html += '</div>';
            calendar.innerHTML = html;
            document.getElementById('calendarTitle').textContent = `Ano: ${year}`;
        }

        // Função para mostrar os dias ao clicar em um mês
        function showDays(year, month) {
            const calendar = document.getElementById('calendar');
            const daysInMonth = new Date(year, month, 0).getDate();
            let html = '<div class="calendar-days">';
            for (let day = 1; day <= daysInMonth; day++) {
                const dateKey = `${year}-${String(month).padStart(2, '0')}-${String(day).padStart(2, '0')}`;
                const dailyRain = rainData[dateKey] || 0;
                html += `<div class="calendar-day">${day} <br> ${dailyRain.toFixed(1)} mm</div>`;
            }
            html += '</div>';
            calendar.innerHTML = html;
            document.getElementById('calendarTitle').textContent = `${new Date(year, month - 1).toLocaleString('pt-BR', { month: 'long', year: 'numeric' })}`;
        }
        // Função para centralizar o mapa no ícone de chuva
        function goToRain() {
            map.setView([rainLat, rainLon], 13);
        }

        // Função para centralizar o mapa em Barra Mansa
        function goHome() {
            map.setView([lat, lon], 13);
        }
    </script>
</body>
</html>
