# Spga

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>GITAM SGPA Calculator</title>

<style>
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  padding: 20px;
  font-family: Arial, sans-serif;
  background: #f3f5fa;
  color: #172033;
}

.container {
  max-width: 850px;
  margin: auto;
}

header {
  background: linear-gradient(120deg, #182b69, #315efb);
  color: white;
  padding: 25px;
  border-radius: 18px;
  text-align: center;
}

.card {
  background: white;
  padding: 20px;
  margin-top: 18px;
  border-radius: 15px;
  box-shadow: 0 4px 15px #0000000c;
}

input, select {
  width: 100%;
  padding: 11px;
  margin: 5px 0 12px;
  border: 1px solid #ddd;
  border-radius: 8px;
}

button {
  padding: 12px 18px;
  border: none;
  border-radius: 8px;
  background: #315efb;
  color: white;
  cursor: pointer;
  font-weight: bold;
  margin: 5px 3px;
}

.subject {
  border: 1px solid #ddd;
  padding: 15px;
  border-radius: 12px;
  margin: 12px 0;
}

.fields {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 8px;
}

.direct .fields {
  grid-template-columns: repeat(2, 1fr);
}

.result {
  display: none;
  background: #e9f9ef;
  padding: 20px;
  border-radius: 12px;
  margin-top: 20px;
}

.result h1 {
  color: #087443;
  font-size: 42px;
}

@media(max-width:600px) {
  .fields {
    grid-template-columns: repeat(2, 1fr);
  }
}
</style>
</head>

<body>
<div class="container">

<header>
  <h1>🎓 GITAM SGPA Calculator</h1>
  <p>Calculate your SGPA easily</p>
</header>

<div class="card">
  <h2>1. Subject Details</h2>

  <label>Number of Regular Subjects</label>
  <input type="number" id="regularCount" value="5" min="0">

  <label>Number of Direct Subjects</label>
  <input type="number" id="directCount" value="2" min="0">

  <button onclick="createSubjects()">Create Subjects</button>
</div>

<div class="card">
  <h2>2. Regular Subjects</h2>
  <p>Formula: 45% + 30% + 25%</p>
  <div id="regularSubjects"></div>
</div>

<div class="card">
  <h2>3. Direct Subjects</h2>
  <div id="directSubjects"></div>

  <button onclick="calculateSGPA()">Calculate SGPA</button>
  <button onclick="location.reload()">Reset</button>
</div>

<div class="result" id="result">
  <h2>Your Final SGPA</h2>
  <h1 id="sgpa">0.000</h1>
  <p id="credits"></p>
  <p id="weighted"></p>
  <div id="details"></div>
</div>

</div>

<script>

// Grade point conversion
const gradePoints = {
  "I": 4,
  "C": 5,
  "B": 6,
  "B+": 7,
  "A": 8,
  "A+": 9,
  "O": 10
};

const grades = Object.keys(gradePoints);

function gradeOptions() {
  return `<option value="">Select Grade</option>` +
    grades.map(g =>
      `<option value="${g}">${g} (${gradePoints[g]})</option>`
    ).join("");
}

// Create subject fields
function createSubjects() {
  let r = Number(document.getElementById("regularCount").value);
  let d = Number(document.getElementById("directCount").value);

  if (!Number.isInteger(r) || !Number.isInteger(d) ||
      r < 0 || d < 0 || r + d === 0 || r > 30 || d > 30) {
    alert("Enter valid subject counts.");
    return;
  }

  let regular = document.getElementById("regularSubjects");
  let direct = document.getElementById("directSubjects");

  regular.innerHTML = "";
  direct.innerHTML = "";
  document.getElementById("result").style.display = "none";

  // Regular subjects
  for (let i = 1; i <= r; i++) {
    regular.innerHTML += `
      <div class="subject">
        <h3>Regular Subject ${i}</h3>

        <label>Subject Name</label>
        <input class="rname" placeholder="Subject name">

        <label>Credits</label>
        <input class="rcredit" type="number" min="0" step="0.5">

        <div class="fields">
          <div>
            <label>Grade 1 (45%)</label>
            <select class="g1">${gradeOptions()}</select>
          </div>

          <div>
            <label>Grade 2 (30%)</label>
            <select class="g2">${gradeOptions()}</select>
          </div>

          <div>
            <label>Grade 3 (25%)</label>
            <select class="g3">${gradeOptions()}</select>
          </div>
        </div>
      </div>
    `;
  }

  // Direct subjects
  for (let i = 1; i <= d; i++) {
    direct.innerHTML += `
      <div class="subject direct">
        <h3>Direct Subject ${i}</h3>

        <label>Subject Name</label>
        <input class="dname" placeholder="Subject name">

        <label>Credits</label>
        <input class="dcredit" type="number" min="0" step="0.5">

        <label>Final Grade</label>
        <select class="dgrade">${gradeOptions()}</select>
      </div>
    `;
  }
}

// Calculate SGPA
function calculateSGPA() {

  let totalCredits = 0;
  let totalPoints = 0;
  let details = "";

  const regularCards =
    document.querySelectorAll("#regularSubjects .subject");

  const directCards =
    document.querySelectorAll("#directSubjects .subject");

  function addSubject(name, credit, grade) {
    let weighted = credit * grade;

    totalCredits += credit;
    totalPoints += weighted;

    details += `
      <p>
        ${name}: ${grade.toFixed(3)} × ${credit}
        = ${weighted.toFixed(3)}
      </p>
    `;
  }

  // Regular subjects calculation
  for (let i = 0; i < regularCards.length; i++) {

    let card = regularCards[i];

    let name = card.querySelector(".rname").value ||
      `Regular Subject ${i+1}`;

    let credit = Number(card.querySelector(".rcredit").value);

    let g1 = card.querySelector(".g1").value;
    let g2 = card.querySelector(".g2").value;
    let g3 = card.querySelector(".g3").value;

    if (!(credit > 0) || !g1 || !g2 || !g3) {
      alert(`Please complete Regular Subject ${i+1}`);
      return;
    }

    let grade =
      (0.45 * gradePoints[g1]) +
      (0.30 * gradePoints[g2]) +
      (0.25 * gradePoints[g3]);

    addSubject(name, credit, grade);
  }

  // Direct subjects calculation
  for (let i = 0; i < directCards.length; i++) {

    let card = directCards[i];

    let name = card.querySelector(".dname").value ||
      `Direct Subject ${i+1}`;

    let credit = Number(card.querySelector(".dcredit").value);
    let grade = card.querySelector(".dgrade").value;

    if (!(credit > 0) || !grade) {
      alert(`Please complete Direct Subject ${i+1}`);
      return;
    }

    addSubject(name, credit, gradePoints[grade]);
  }

  if (totalCredits === 0) {
    alert("Please add subjects.");
    return;
  }

  // Final SGPA
  let sgpa = totalPoints / totalCredits;

  document.getElementById("sgpa").innerText =
    sgpa.toFixed(3);

  document.getElementById("credits").innerText =
    "Total Credits: " + totalCredits;

  document.getElementById("weighted").innerText =
    "Total Weighted Points: " + totalPoints.toFixed(3);

  document.getElementById("details").innerHTML = details;

  document.getElementById("result").style.display = "block";

  document.getElementById("result").scrollIntoView({
    behavior: "smooth"
  });
}

// Initial subjects
createSubjects();

</script>
</body>
</html>
