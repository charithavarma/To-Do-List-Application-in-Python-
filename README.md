# To-Do-List-Application-in-Python-
import tkinter as tk
from tkinter import messagebox
from datetime import datetime

class To_DoApp:
    DATE_FORMAT = "%Y-%m-%d"

    def __init__(self, master):
        self.master = master
        self.master.title("To-Do List App")
        self.todo_list = To_DoList()

        tk.Label(master, text="Task").grid(row=0, column=0, padx=10, pady=5)
        tk.Label(master, text="Description").grid(row=0, column=1, padx=10, pady=5)
        tk.Label(master, text="Due Date (YYYY-MM-DD)").grid(row=0, column=2, padx=10, pady=5)

        self.task_entry = tk.Entry(master, width=20)
        self.task_entry.grid(row=1, column=0, padx=10, pady=5)

        self.description_entry = tk.Entry(master, width=20)
        self.description_entry.grid(row=1, column=1, padx=10, pady=5)

        self.due_date_entry = tk.Entry(master, width=20)
        self.due_date_entry.grid(row=1, column=2, padx=10, pady=5)

        self.add_button = tk.Button(master, text="Add Task", command=self.add_task)
        self.add_button.grid(row=1, column=3, padx=10, pady=5)

        self.remove_button = tk.Button(master, text="Remove Task", command=self.remove_task)
        self.remove_button.grid(row=2, column=0, padx=10, pady=5)

        self.complete_button = tk.Button(master, text="Mark as Completed", command=self.mark_as_completed)
        self.complete_button.grid(row=2, column=1, padx=10, pady=5)

        self.task_listbox = tk.Listbox(master, width=50, height=10)
        self.task_listbox.grid(row=3, column=0, columnspan=4, padx=10, pady=5)

        tk.Label(master, text="Completed Tasks").grid(row=4, column=0, columnspan=4, pady=5)

        self.completed_listbox = tk.Listbox(master, width=50, height=10)
        self.completed_listbox.grid(row=5, column=0, columnspan=4, padx=10, pady=5)

        self.populate_task_list()

    def add_task(self):
        task = self.task_entry.get()
        description = self.description_entry.get()
        due_date_str = self.due_date_entry.get()

        try:
            due_date = datetime.strptime(due_date_str, self.DATE_FORMAT).date() if due_date_str else None
        except ValueError:
            messagebox.showwarning("Warning", "Invalid date format. Please use YYYY-MM-DD.")
            return

        if task:
            self.todo_list.add_task(task, description, due_date)
            self.task_entry.delete(0, tk.END)
            self.description_entry.delete(0, tk.END)
            self.due_date_entry.delete(0, tk.END)
            self.populate_task_list()
        else:
            messagebox.showwarning("Warning", "Please enter a task.")

    def remove_task(self):
        selected_index = self.task_listbox.curselection()
        if selected_index:
            self.todo_list.remove_task(selected_index[0])
            self.populate_task_list()
        else:
            messagebox.showwarning("Warning", "Please select a task to remove.")

    def mark_as_completed(self):
        selected_index = self.task_listbox.curselection()
        if selected_index:
            self.todo_list.mark_as_completed(selected_index[0])
            self.populate_task_list()
        else:
            messagebox.showwarning("Warning", "Please select a task to mark as completed.")

    def populate_task_list(self):
        self.task_listbox.delete(0, tk.END)
        self.completed_listbox.delete(0, tk.END)
        for task_info in self.todo_list.tasks:
            status = "Completed" if task_info["completed"] else "Not Completed"
            desc = task_info['description'] if task_info["description"] else ""
            due_date = task_info['due_date'].strftime(self.DATE_FORMAT) if task_info["due_date"] else "No Due Date"
            task_details = f"{task_info['task']:<20} {desc:<20} {due_date:<20} ({status})"
            if task_info["completed"]:
                self.completed_listbox.insert(tk.END, task_details)
            else:
                self.task_listbox.insert(tk.END, task_details)

class To_DoList:
    def __init__(self):
        self.tasks = []

    def add_task(self, task, description=None, due_date=None):
        task_info = {"task": task, "completed": False, "description": description, "due_date": due_date}
        self.tasks.append(task_info)

    def remove_task(self, index):
        if 0 <= index < len(self.tasks):
            del self.tasks[index]

    def mark_as_completed(self, index):
        if 0 <= index < len(self.tasks):
            self.tasks[index]["completed"] = True

# Main application loop
if __name__ == "__main__":
    root = tk.Tk()
    app = To_DoApp(root)
    root.mainloop()
