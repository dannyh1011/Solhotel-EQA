<html lang="zh-Hant">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>QA 自動回覆</title>
  <style>
    html, body {
      margin: 0;
      padding: 0;
      width: 100%;
      height: 100%;
      background-color: #1a1616;
      display: flex;
      justify-content: center;
      align-items: center;
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    }

    .qa-container {
      background-color: #2c2c2c;
      border: 6px solid #a67c52;
      border-radius: 20px;
      padding: 30px 25px;
      width: 90%;
      max-width: 1000px;
      box-sizing: border-box;
      overflow-y: auto;
      max-height: 95vh;
    }

    .qa-item {
      margin-bottom: 20px;
    }

    .question {
      cursor: pointer;
      font-size: 1.5rem;
      color: #FFC107;
      margin-bottom: 8px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      background-color: #444;
      padding: 12px 16px;
      border-radius: 10px;
      transition: background 0.3s;
    }

    .question:hover {
      background-color: #555;
    }

    .arrow {
      font-size: 1.2rem;
      color: #FFC107;
    }

    .answer {
      max-height: 0;
      overflow: hidden;
      transition: max-height 0.4s ease, padding 0.3s ease;
      font-size: 1.1rem;
      color: #ffffff;
      padding-left: 20px;
      line-height: 1.6;
      background-color: #3a3a3a;
      border-radius: 8px;
      padding: 0 16px;
    }

    .answer.open {
      max-height: 800px;
      padding: 15px 16px;
    }

    @media (max-width: 600px) {
      .qa-container {
        padding: 20px 15px;
      }
      .question {
        font-size: 1.2rem;
      }
      .answer {
        font-size: 1rem;
      }
    }
  </style>
</head>
<body>

  <div class="qa-container" id="qa">
    <!-- QA 內容將由 JS 注入 -->
  </div>

  <script>
    const qaData = [
     {
    q: "Is there parking and EV charging available?",
    a: `Free flat parking is available for hotel guests on B1 (height limit 2.2m). You may park directly upon arrival and take the elevator to the lobby for check-in. Parking is limited and cannot be reserved in advance.<br><br>
        ⭐ Parking is in high demand during Chinese New Year and weekends. We apologize for any inconvenience caused by delays.<br>
        ⭐ No EV charging station available in the hotel.`
  },
  {
    q: "Breakfast content and serving hours? Is vegetarian food available?",
    a: `▪ Buffet with Chinese, Western, and Japanese selections.<br>
        ▪ Served 6:30 ~ 10:00. Please provide your room number and number of guests.<br>
        ▪ Buffet style with only a limited selection of vegetarian options.<br><br>
       ⭐ During peak holiday periods, dining time may be limited to 1 hour.`
  },
  {
    q: "Do you provide baby cots and bed guards?",
    a: `We offer free rental of baby cots and bed guards. Please note:<br>
        ▪ Baby cots are for infants under 1 year old only, for safety reasons.<br>
        ▪ Children over 1 year are advised to use bed guards (must be installed before check-in).<br>
        ▪ Limited availability – please request at time of booking.<br>
        ▪ Baby cots are not available in Standard Double Rooms.`
  },
  {
    q: "Do you offer baby bathtubs and bottle sterilizers?",
    a: `▪ Yes, baby bathtubs (with small stool) and bottle sterilizers are available for free rental.<br>
        ▪ Quantities are limited – please reserve in advance.`
  },
  {
    q: "Do bathrooms have bathtubs?",
    a: `▪ Standard Double Rooms: Small seated bathtubs only.<br>
        ▪ Deluxe Rooms and above: Standard single bathtubs included.`
  },
  {
    q: "What are the child surcharges?",
    a: `▪ Infants under 1 may use baby cots (limited, by request).<br>
        ▪ Under 109 cm: Free of charge<br>
        ▪ 110–139 cm: NT$250<br>
        ▪ Over 140 cm: Charged as adult, extra bed recommended<br>`
  },
  {
    q: "What are the charges for additional adults?",
    a: `▪ With breakfast : NT$500 <br><br>
        ▪ Without breakfast : NT$400 <br><br>
        ⭐ Standard Double Rooms do not support extra beds.`
  },

 {
    q: "Is there an age restriction for staying?",
    a: `▪ Guests must be at least ⭐20 years old to check in.`
  },
      
  {
    q: "What is the definition of weekday and weekend rates?",
    a: `▪ Weekdays: Sunday to Friday<br>▪ Weekends: Saturday and public holidays`
  },
  {
    q: "How to get there by public transportation? Is there shuttle service?",
    a: `▪ 8-minute walk from Hsinchu TRA Station (via Dong Men Elementary School rear gate)<br>
        ▪ From HSR Hsinchu Station → 2-min walk over the skybridge to Liujia TRA Station → transfer to Hsinchu TRA Station → 8-min walk to hotel<br>
        ▪ No shuttle service provided.`
  },

      {
        q: "Coin-operated Self-service Washer / Dryer – Fee Information？",
        a: `▪ Washing Machine (Wash + Spin Dry)<br>
            . Cost: NT$50 (Insert five NT$10 coins)<br>
            . Time Required: Approx. 40 minutes<br><br>
      
            ▪ Dryer<br>
            . Cost: NT$10 per 8 minutes (Insert one NT$10 coin per cycle)<br>
            . Recommended Time: About 40 minutes or more depending on load (At least five NT$10 coins)<br>`
      },
  {
    q: "What are the check-in/out times and luggage storage policy?",
    a: `▪ Check-in: after 15:00; Check-out: before 11:00.<br>
        ⭐ Run of House Check-in after 19:00.<br>
        ▪ Luggage storage available at the front desk before check-in and after check-out.`
  }

    ];

    const container = document.getElementById('qa');

    qaData.forEach((item, index) => {
      const qaItem = document.createElement('div');
      qaItem.className = 'qa-item';

      const question = document.createElement('div');
      question.className = 'question';
      question.innerHTML = `・${item.q} <span class="arrow">▼</span>`;
      question.onclick = () => toggleAnswer(index);

      const answer = document.createElement('div');
      answer.className = 'answer';
      answer.innerHTML = item.a;

      qaItem.appendChild(question);
      qaItem.appendChild(answer);
      container.appendChild(qaItem);
    });

    function toggleAnswer(index) {
      const answers = document.querySelectorAll('.answer');
      const arrows = document.querySelectorAll('.arrow');
      const answer = answers[index];
      const arrow = arrows[index];
      const isOpen = answer.classList.contains('open');

      answer.classList.toggle('open');
      arrow.textContent = isOpen ? '▼' : '▲';
    }

    // 預設展開第一個問題
    window.onload = () => toggleAnswer(0);
  </script>
</body>
</html>
