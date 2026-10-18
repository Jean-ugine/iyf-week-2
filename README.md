
const form = document.getElementById("todo-form");
const input = document.getElementById("todo-input");
const formError = document.getElementById("form-error");
const todoList = document.getElementById("todo-list");
const itemsLeft = document.getElementById("items-left");
const filters = document.querySelectorAll(".filter");
const clearCompletedBtn = document.getElementById("clear-completed");

let todos = [];
let currentFilter = "all";
let nextId = 1;

// STEP 1A: Create a task element
function createTodoElement(todo) {
    const li = document.createElement("li");
    li.className = "todo-item";
    li.dataset.id = todo.id;

    if (todo.completed) {
        li.classList.add("completed");
    }

    const checkbox = document.createElement("input");
    checkbox.type = "checkbox";
    checkbox.className = "toggle";
    checkbox.checked = todo.completed;
    checkbox.setAttribute("aria-label", `Complete "${todo.text}"`);

    const span = document.createElement("span");
    span.className = "todo-text";
    span.textContent = todo.text;

    const deleteBtn = document.createElement("button");
    deleteBtn.type = "button";
    deleteBtn.className = "delete";
    deleteBtn.textContent = "✕";
    deleteBtn.setAttribute("aria-label", `Delete "${todo.text}"`);

    li.append(checkbox, span, deleteBtn);

    return li;
}

// STEP 1B: Filter tasks
function visibleTodos() {
    if (currentFilter === "active") {
        return todos.filter(todo => !todo.completed);
    }

    if (currentFilter === "completed") {
        return todos.filter(todo => todo.completed);
    }

    return todos;
}

// STEP 1C: Display tasks
function render() {
    todoList.replaceChildren(
        ...visibleTodos().map(createTodoElement)
    );

    updateStats();
}

// STEP 1D: Update remaining task count
function updateStats() {
    const remaining = todos.filter(todo => !todo.completed).length;

    itemsLeft.textContent =
        `${remaining} ${remaining === 1 ? "item" : "items"} left`;
}

// STEP 2A: Add a task to the array
function addTodo(text) {
    todos.push({
        id: nextId++,
        text: text,
        completed: false
    });

    render();
}

// STEP 2B: FORM SUBMIT EVENT LISTENER
form.addEventListener("submit", function(event) {
    event.preventDefault();

    const text = input.value.trim();

    if (text === "") {
        formError.textContent = "Type a task first.";
        return;
    }

    formError.textContent = "";

    addTodo(text);

    input.value = "";
    input.focus();
});

// STEP 3A: Complete or uncomplete a task
function toggleTodo(id) {
    todos = todos.map(todo =>
        todo.id === id
            ? { ...todo, completed: !todo.completed }
            : todo
    );

    render();
}

// Delete a task
function deleteTodo(id) {
    todos = todos.filter(todo => todo.id !== id);

    render();
}

// STEP 3B: ONE DELEGATED CLICK EVENT LISTENER
todoList.addEventListener("click", function(event) {
    const item = event.target.closest(".todo-item");

    if (!item) return;

    const id = Number(item.dataset.id);

    // Check delete first
    if (event.target.closest(".delete")) {
        deleteTodo(id);
        return;
    }

    toggleTodo(id);
});

// STEP 4A: Filter tasks
function filterTodos(filter) {
    currentFilter = filter;

    filters.forEach(btn => {
        const isActive = btn.dataset.filter === filter;

        btn.classList.toggle("active", isActive);
        btn.setAttribute("aria-pressed", String(isActive));
    });

    render();
}

// STEP 4B: FILTER BUTTON EVENT LISTENERS
filters.forEach(btn => {
    btn.addEventListener("click", function() {
        filterTodos(btn.dataset.filter);
    });
});

// STEP 4C: Clear completed tasks
function clearCompleted() {
    todos = todos.filter(todo => !todo.completed);

    render();
}

clearCompletedBtn.addEventListener("click", clearCompleted);

// INITIALIZE THE TODO APP
render();

