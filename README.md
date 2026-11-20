<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>SGPA Calculator</title>

<style>
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  background: #0b1220;
  color: white;
  font-family: Arial, sans-serif;
}

.app {
  max-width: 520px;
  margin: auto;
  padding: 16px;
}

header {
  background: linear-gradient(135deg, #1b2b4a, #526df5);
  padding: 25px 18px;
  border-radius: 20px;
  margin-bottom: 15px;
}

header h1 {
  margin: 8px 0;
}

header p {
  color: #dce5ff;
  font-size: 13px;
}

.panel {
  background: #131e30;
  border: 1px solid #293950;
  padding: 16px;
  border-radius: 17px;
  margin: 14px 0;
}

h2 {
  font-size: 18px;
  margin-top: 0;
}

.sub {
  font-size: 12px;
  color: #9eacc2;
}

label {
  display: block;
  font-size: 12px;
  margin: 8px 0 5px;
  color: #c4cee0;
}

input, select {
  width: 100%;
  padding: 12px;
  min-height: 45px;
  border: 1px solid #3a4b64;
  border-radius: 10px;
  background: #0e1828;
  color: white;
  font-size: 16px;
}

button {
  border: 0;
  border-radius: 11px;
  padding: 13px;
  font-weight: bold;
  cursor: pointer;
}

.primary {
  background: #7895ff;
  color: #101a35;
  width: 100%;
  margin-top: 14px;
}

.secondary, .back {
  background: #263750;
  color: white;
}

.back {
  margin-bottom: 10px;
}

.hidden {
  display: none !important;
}

.count-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 10px;
}

.subject {
  background: #101a2a;
  border: 1px solid #293950;
  border-radius: 13px;
  padding: 13px;
  margin-top: 12px;
}

.subject-head {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.remove {
  background: #482731;
  color: #ffb5bd;
  padding: 8px;
}

.credit-grid, .direct-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 9px;
}

.grade-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 7px;
}

.grade-grid select {
  padding: 8px 4px;
  font-size: 13px;
}

.preview {
  color: #9eacc2;
  font-size: 12px;
}

.preview b {
  color: #6ee7ad;
}

.result {
  display: none;
  background: #102c28;
  border-color: #286449;
}

.sgpa {
  font-size: 48px;
  color: #6ee7ad;
  font-weight: bold;
}

.metric {
  background: #0c211f;
  border: 1px solid #285044;
  padding: 12px;
  border-radius: 10px;
  margin-top: 8px;
}

.metric span {
  font-size: 12px;
  color: #b6d8c8;
}

.metric b {
  display: block;
  font-size: 20px;
  margin-top: 5px;
}

.detail {
  display: flex;
  justify-content: space-between;
  padding: 10px 0;
  border-bottom: 1px solid #285044;
  font-size: 13px;
}

.detail b {
  color: #6ee7ad;
}

.error {
  display: none;
  color: #ffaaaa;
  margin-top: 12px;
  font-size: 13px;
}

footer {
  text-align: center;
  color: #9eacc2;
  font-size: 11px;
  padding: 15px;
}
</style>
</head>

<body>

<main class="app">

<header>
  <small>STUDENT TOOL</small>
  <h1>🎓 SGPA Calculator</h1>
  <p>Calculate your semester SGPA easily.</p>
</header>

<!-- FIRST SCREEN -->

<section id="page1">

  <div class="panel">
    <h2>How many subjects?</h2>
    <p class="sub">Enter your exact subject count.</p>

    <div class="count-grid">
      <div>
        <label>Regular Subjects</label>
        <input
          type="number"
          id="regularCount"
          min="0"
          max="30"
          placeholder="Example: 4"
        >
      </div>

      <div>
        <label>Direct Subjects</label>
        <input
          type="number"
          id="directCount"
          min="0"
          max="30"
          placeholder="Example: 2"
        >
      </div>
    </div>

    <p class="error" id="countError"></p>

    <button class="primary" onclick="generateSubjects()">
      Continue →
    </button>
  </div>

</section>

<!-- SECOND SCREEN -->

<section id="page2" class="hidden">

  <button class="back" onclick="goBack()">← Back</button>

  <div class="panel">
    <h2>Enter Subject Details</h2>
    <p class="sub">
      Enter credits and grades for each subject.
    </p>

    <h3 id="regularHeading"></h3>
    <div id="regularSubjects"></div>

    <h3 id="directHeading"></h3>
    <div id="directSubjects"></div>

    <p class="error" id="formError"></p>

    <button class="primary" onclick="calculateSGPA()">
      Calculate SGPA
    </button>
  </div>

  <div class="panel result" id="result">
    <p class="sub">YOUR SEMESTER SGPA</p>
    <div class="sgpa" id="sgpaValue">0.000</div>

    <div class="metric">
      <span>Total Credits</span>
      <b id="totalCredits">0</b>
    </div>

    <div class="metric">
      <span>Total Weighted Grade Points</span>
      <b id="totalPoints">0</b>
    </div>

    <h3>Subject-wise Details</h3>
    <div id="resultDetails"></div>

    <button class="secondary" onclick="resetCalculator()">
      Start Again
    </button>
  </div>

</section>

<footer>
  SGPA Calculator | Made for students
</footer>

</main>

<script>

const gradePoints = {
  "I": 4,
  "C": 5,
  "B": 6,
  "B+": 7,
  "A": 8,
  "A+": 9,
  "O": 10
};

let regularNumber = 0;
let directNumber = 0;

function gradeOptions() {
  return `
    <option value="">Select Grade</option>
    <option value="I">I - 4</option>
    <option value="C">C - 5</option>
    <option value="B">B - 6</option>
    <option value="B+">B+ - 7</option>
    <option value="A">A - 8</option>
    <option value="A+">A+ - 9</option>
    <option value="O">O - 10</option>
  `;
}

function generateSubjects() {

  const r = Number(document.getElementById("regularCount").value);
  const d = Number(document.getElementById("directCount").value);

  const error = document.getElementById("countError");

  if (
    !Number.isInteger(r) ||
    !Number.isInteger(d) ||
    r < 0 || d < 0 ||
    r > 30 || d > 30 ||
    r + d === 0
  ) {
    error.style.display = "block";
    error.innerText =
      "Enter valid subject counts (1 to 30 total subjects).";
    return;
  }

  error.style.display = "none";

  regularNumber = r;
  directNumber = d;

  document.getElementById("regularHeading").innerText =
    `Regular Subjects (${r})`;

  document.getElementById("directHeading").innerText =
    `Direct Subjects (${d})`;

  const regularContainer =
    document.getElementById("regularSubjects");

  const directContainer =
    document.getElementById("directSubjects");

  regularContainer.innerHTML = "";
  directContainer.innerHTML = "";

  // Generate EXACT regular subject count

  for (let i = 1; i <= r; i++) {

    regularContainer.innerHTML += `
      <div class="subject regular-subject">

        <div class="subject-head">
          <h3>Regular Subject ${i}</h3>
          <button class="remove"
            onclick="removeSubject(this, 'regular')">
            Remove
          </button>
        </div>

        <label>Subject Name</label>
        <input type="text"
          class="subject-name"
          placeholder="Enter subject name">

        <label>Credits</label>
        <input type="number"
          class="credits"
          min="0.1"
          step="0.1"
          placeholder="Enter credits">

        <label>Grade 1</label>
        <select class="grade1">
          ${gradeOptions()}
        </select>

        <label>Grade 2</label>
        <select class="grade2">
          ${gradeOptions()}
        </select>

        <label>Grade 3</label>
        <select class="grade3">
          ${gradeOptions()}
        </select>

        <p class="preview">
          Final Grade Points:
          <b class="final-points">--</b>
        </p>

      </div>
    `;
  }

  // Generate EXACT direct subject count

  for (let i = 1; i <= d; i++) {

    directContainer.innerHTML += `
      <div class="subject direct-subject">

        <div class="subject-head">
          <h3>Direct Subject ${i}</h3>
          <button class="remove"
            onclick="removeSubject(this, 'direct')">
            Remove
          </button>
        </div>

        <label>Subject Name</label>
        <input type="text"
          class="subject-name"
          placeholder="Enter subject name">

        <label>Credits</label>
        <input type="number"
          class="credits"
          min="0.1"
          step="0.1"
          placeholder="Enter credits">

        <label>Final Grade</label>
        <select class="direct-grade">
          ${gradeOptions()}
        </select>

        <p class="preview">
          Final Grade Points:
          <b class="final-points">--</b>
        </p>

      </div>
    `;
  }

  document.getElementById("page1").classList.add("hidden");
  document.getElementById("page2").classList.remove("hidden");

  document.getElementById("result").style.display = "none";
  window.scrollTo(0, 0);
}

function goBack() {
  document.getElementById("page2").classList.add("hidden");
  document.getElementById("page1").classList.remove("hidden");
  window.scrollTo(0, 0);
}

function removeSubject(button, type) {

  button.closest(".subject").remove();

  const regularSubjects =
    document.querySelectorAll(".regular-subject");

  const directSubjects =
    document.querySelectorAll(".direct-subject");

  regularNumber = regularSubjects.length;
  directNumber = directSubjects.length;

  document.getElementById("regularHeading").innerText =
    `Regular Subjects (${regularNumber})`;

  document.getElementById("directHeading").innerText =
    `Direct Subjects (${directNumber})`;

  // Update subject numbers

  regularSubjects.forEach((subject, index) => {
    subject.querySelector("h3").innerText =
      `Regular Subject ${index + 1}`;
  });

  directSubjects.forEach((subject, index) => {
    subject.querySelector("h3").innerText =
      `Direct Subject ${index + 1}`;
  });
}

function calculateSGPA() {

  const error = document.getElementById("formError");
  error.style.display = "none";

  let totalCredits = 0;
  let totalPoints = 0;
  let details = "";

  const regularSubjects =
    document.querySelectorAll(".regular-subject");

  const directSubjects =
    document.querySelectorAll(".direct-subject");

  if (
    regularSubjects.length + directSubjects.length === 0
  ) {
    error.style.display = "block";
    error.innerText = "Add at least one subject.";
    return;
  }

  function processSubject(subject, type) {

    const name =
      subject.querySelector(".subject-name").value.trim();

    const credits =
      Number(subject.querySelector(".credits").value);

    if (!name || !credits || credits <= 0) {
      return false;
    }

    let finalGradePoints;

    if (type === "regular") {

      const g1 = subject.querySelector(".grade1").value;
      const g2 = subject.querySelector(".grade2").value;
      const g3 = subject.querySelector(".grade3").value;

      if (!g1 || !g2 || !g3) {
        return false;
      }

      finalGradePoints =
        (0.45 * gradePoints[g1]) +
        (0.30 * gradePoints[g2]) +
        (0.25 * gradePoints[g3]);

    } else {

      const grade =
        subject.querySelector(".direct-grade").value;

      if (!grade) {
        return false;
      }

      finalGradePoints = gradePoints[grade];
    }

    const weightedPoints =
      finalGradePoints * credits;

    totalCredits += credits;
    totalPoints += weightedPoints;

    subject.querySelector(".final-points").innerText =
      finalGradePoints.toFixed(3);

    details += `
      <div class="detail">
        <span>${escapeHTML(name)} (${credits} credits)</span>
        <b>${finalGradePoints.toFixed(3)}</b>
      </div>
    `;

    return true;
  }

  for (const subject of regularSubjects) {
    if (!processSubject(subject, "regular")) {
      error.style.display = "block";
      error.innerText =
        "Please fill all regular subject details correctly.";
      return;
    }
  }

  for (const subject of directSubjects) {
    if (!processSubject(subject, "direct")) {
      error.style.display = "block";
      error.innerText =
        "Please fill all direct subject details correctly.";
      return;
    }
  }

  if (totalCredits <= 0) {
    error.style.display = "block";
    error.innerText = "Total credits must be greater than zero.";
    return;
  }

  const sgpa = totalPoints / totalCredits;

  document.getElementById("sgpaValue").innerText =
    sgpa.toFixed(3);

  document.getElementById("totalCredits").innerText =
    totalCredits.toFixed(2);

  document.getElementById("totalPoints").innerText =
    totalPoints.toFixed(3);

  document.getElementById("resultDetails").innerHTML =
    details;

  document.getElementById("result").style.display = "block";

  document.getElementById("result").scrollIntoView({
    behavior: "smooth"
  });
}

function escapeHTML(text) {
  return text.replace(/[&<>"']/g, function(char) {
    return {
      "&": "&amp;",
      "<": "&lt;",
      ">": "&gt;",
      '"': "&quot;",
      "'": "&#039;"
    }[char];
  });
}

function resetCalculator() {

  document.getElementById("result").style.display = "none";

  document.getElementById("regularSubjects").innerHTML = "";
  document.getElementById("directSubjects").innerHTML = "";

  document.getElementById("regularCount").value = "";
  document.getElementById("directCount").value = "";

  document.getElementById("page2").classList.add("hidden");
  document.getElementById("page1").classList.remove("hidden");

  window.scrollTo(0, 0);
}

</script>

</body>
</html>
