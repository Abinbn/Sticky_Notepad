document.addEventListener("DOMContentLoaded", function () {
  const notesContainer = document.getElementById("notesContainer");
  const addBtn = document.getElementById("addBtn");

  let notes = [];

  addBtn.addEventListener("click", createNote);

  function createNote() {
    const note = document.createElement("div");
    note.classList.add("note");
    note.innerHTML = `
      <span class="close-btn">&times;</span>
      <textarea></textarea>
    `;
    notesContainer.appendChild(note);
    notes.push(note);
    setPosition(note);
    const textarea = note.querySelector("textarea");
    textarea.focus();
    note.querySelector(".close-btn").addEventListener("click", () => deleteNote(note));
    textarea.addEventListener("input", () => saveNotes());
  }

  function setPosition(note) {
    const randomX = Math.floor(Math.random() * (window.innerWidth - 250));
    const randomY = Math.floor(Math.random() * (window.innerHeight - 250));
    note.style.left = `${randomX}px`;
    note.style.top = `${randomY}px`;
  }

  function deleteNote(note) {
    notesContainer.removeChild(note);
    notes = notes.filter((item) => item !== note);
    saveNotes();
  }

  function saveNotes() {
    const notesData = notes.map((note) => note.querySelector("textarea").value);
    document.cookie = `notes=${JSON.stringify(notesData)}; expires=Fri, 31 Dec 9999 23:59:59 GMT;`;
  }

  function loadNotes() {
    const cookies = document.cookie.split(";").map((cookie) => cookie.trim());
    const notesCookie = cookies.find((cookie) => cookie.startsWith("notes="));
    if (notesCookie) {
      const notesData = JSON.parse(notesCookie.split("=")[1]);
      notesData.forEach((noteText) => {
        const note = document.createElement("div");
        note.classList.add("note");
        note.innerHTML = `
          <span class="close-btn">&times;</span>
          <textarea>${noteText}</textarea>
        `;
        notesContainer.appendChild(note);
        notes.push(note);
        setPosition(note);
        const textarea = note.querySelector("textarea");
        textarea.focus();
        note.querySelector(".close-btn").addEventListener("click", () => deleteNote(note));
        textarea.addEventListener("input", () => saveNotes());
      });
    }
  }

  loadNotes();

  // Drag and drop functionality
  notesContainer.addEventListener("mousedown", (e) => {
    const target = e.target;
    if (target.classList.contains("note")) {
      target.style.cursor = "grabbing";
      let offsetX = e.clientX - target.getBoundingClientRect().left;
      let offsetY = e.clientY - target.getBoundingClientRect().top;

      function onMouseMove(e) {
        const x = e.clientX - offsetX;
        const y = e.clientY - offsetY;
        target.style.left = `${x}px`;
        target.style.top = `${y}px`;
      }

      function onMouseUp() {
        target.style.cursor = "grab";
        document.removeEventListener("mousemove", onMouseMove);
        document.removeEventListener("mouseup", onMouseUp);
      }

      document.addEventListener("mousemove", onMouseMove);
      document.addEventListener("mouseup", onMouseUp);
    }
  });
});

