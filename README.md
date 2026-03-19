#<!DOCTYPE html>
<html>
<head>
  <title>Prince Hair Salon Booking</title>
  <style>
    body { font-family: Arial; text-align: center; }

    h1 { margin-bottom: 5px; }
    p { margin-top: 0; color: gray; }

    .day {
      margin-top: 20px;
    }

    .slot {
      padding: 10px;
      margin: 5px;
      border: 1px solid #333;
      cursor: pointer;
      width: 180px;
      display: inline-block;
    }

    .booked {
      background-color: red;
      color: white;
      cursor: not-allowed;
    }

    .break {
      background-color: #ddd;
      cursor: not-allowed;
    }

  </style>
</head>
<body>

<h1>Prince Hair Salon</h1>
<p>📞 Contact: 9848133229</p>
<p>⏰ Timings: 11:00 AM – 10:00 PM (Lunch Break: 2–3 PM)</p>

<div id="calendar"></div>

<script>
  const container = document.getElementById("calendar");

  let startHour = 11;
  let endHour = 22;

  let bookings = JSON.parse(localStorage.getItem("bookings")) || {};

  function getNext7Days() {
    let days = [];
    let today = new Date();

    for (let i = 0; i < 7; i++) {
      let d = new Date();
      d.setDate(today.getDate() + i);

      let dateStr = d.toISOString().split('T')[0];
      let display = d.toDateString();

      days.push({ key: dateStr, label: display });
    }

    return days;
  }

  function createCalendar() {
    container.innerHTML = "";

    let days = getNext7Days();

    days.forEach(day => {
      let dayDiv = document.createElement("div");
      dayDiv.className = "day";

      let title = document.createElement("h3");
      title.innerText = day.label;

      dayDiv.appendChild(title);

      for (let h = startHour; h < endHour; h++) {
        for (let m of [0, 30]) {

          // Skip lunch break (2 PM to 3 PM)
          if (h === 14) {
            let breakDiv = document.createElement("div");
            breakDiv.className = "slot break";
            breakDiv.innerText = "2:00 PM - Break";
            dayDiv.appendChild(breakDiv);
            break;
          }

          let time = formatTime(h, m);
          let key = day.key + "_" + time;

          let div = document.createElement("div");
          div.className = "slot";

          if (bookings[key]) {
            div.classList.add("booked");
            div.innerText = `${time} - ${bookings[key].name}`;
          } else {
            div.innerText = time;
          }

          div.onclick = function () {
            if (bookings[key]) return;

            let name = prompt("Enter customer name:");
            if (!name) return;

            let phone = prompt("Enter phone number:");

            bookings[key] = { name, phone };
            localStorage.setItem("bookings", JSON.stringify(bookings));

            createCalendar();
          };

          dayDiv.appendChild(div);
        }
      }

      container.appendChild(dayDiv);
    });
  }

  function formatTime(hour, minute) {
    let ampm = hour >= 12 ? "PM" : "AM";
    let h = hour > 12 ? hour - 12 : hour;
    return `${h}:${minute === 0 ? "00" : "30"} ${ampm}`;
  }

  createCalendar();
</script>

</body>
</html>
