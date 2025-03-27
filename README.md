import tkinter as tk
from tkinter import messagebox, ttk
import matplotlib.pyplot as plt
import numpy as np
import random

# ===========================
# Module 1: AI-Based Scheduler
# ===========================
def train_ml_scheduler(training_data):
    """ Train a dummy ML model (Replace with actual AI model later) """
    model = {"average": sum(training_data) / len(training_data)}
    return model

def ai_based_scheduling(model, disk_requests):
    """ AI-based disk scheduling (sorts requests as a placeholder for now) """
    return sorted(disk_requests)

# ===========================
# Module 2: Disk Scheduling Algorithms
# ===========================
def fcfs(disk_requests):
    """ First-Come, First-Served (FCFS) Scheduling """
    return disk_requests

def sstf(initial, disk_requests):
    """ Shortest Seek Time First (SSTF) Scheduling """
    requests = disk_requests[:]
    position = initial
    sequence = []
    while requests:
        closest = min(requests, key=lambda x: abs(x - position))
        sequence.append(closest)
        position = closest
        requests.remove(closest)
    return sequence

def scan(initial, disk_requests, disk_size=200):
    """ SCAN Algorithm (Elevator Algorithm) """
    left = sorted([x for x in disk_requests if x < initial])
    right = sorted([x for x in disk_requests if x >= initial])
    return left[::-1] + right

def cscan(initial, disk_requests, disk_size=200):
    """ C-SCAN Algorithm (Circular SCAN) """
    left = sorted([x for x in disk_requests if x < initial])
    right = sorted([x for x in disk_requests if x >= initial])
    return right + left

# ===========================
# Module 3: Simulation & Visualization
# ===========================
def simulate_disk_movement(initial, schedule):
    """ Simulate disk head movement and calculate total head movement """
    positions = [initial] + schedule
    distances = [abs(positions[i+1] - positions[i]) for i in range(len(positions)-1)]
    total_movement = sum(distances)
    return positions, total_movement

def visualize_movement(positions):
    """ Visualize disk head movement """
    steps = list(range(len(positions)))
    plt.figure("Disk Head Movement")
    plt.plot(steps, positions, marker='o', linestyle='-', color='b')
    plt.title("Disk Head Movement Simulation")
    plt.xlabel("Step")
    plt.ylabel("Disk Cylinder")
    plt.grid(True)
    plt.show()

# ===========================
# Module 4: GUI Integration
# ===========================
def run_simulation():
    """ Runs the selected scheduling algorithm and displays results """
    try:
        initial = int(initial_entry.get())
        requests = list(map(int, request_entry.get().split(',')))
        algorithm = algorithm_choice.get()
    except Exception:
        messagebox.showerror("Input Error", "Enter valid integers (comma-separated for disk requests).")
        return

    # Train AI model with random data (for now, replace with actual training data)
    training_data = [random.randint(0, 200) for _ in range(10)]
    model = train_ml_scheduler(training_data)

    # Select scheduling algorithm
    if algorithm == "AI-Based":
        schedule = ai_based_scheduling(model, requests)
    elif algorithm == "FCFS":
        schedule = fcfs(requests)
    elif algorithm == "SSTF":
        schedule = sstf(initial, requests)
    elif algorithm == "SCAN":
        schedule = scan(initial, requests)
    elif algorithm == "C-SCAN":
        schedule = cscan(initial, requests)
    else:
        messagebox.showerror("Error", "Invalid Algorithm Selected")
        return

    # Simulate disk head movement
    positions, total_movement = simulate_disk_movement(initial, schedule)

    # Display results
    result_text.delete("1.0", tk.END)
    result_text.insert(tk.END, f"Algorithm Used: {algorithm}\n")
    result_text.insert(tk.END, f"Optimal Schedule: {schedule}\n")
    result_text.insert(tk.END, f"Total Head Movement: {total_movement}\n")

    # Visualize movement
    visualize_movement(positions)

# GUI Setup
root = tk.Tk()
root.title("AI-Based Disk Scheduling Simulator")
root.geometry("500x400")

# Labels & Inputs
tk.Label(root, text="Initial Head Position:").grid(row=0, column=0, padx=5, pady=5, sticky="e")
initial_entry = tk.Entry(root)
initial_entry.grid(row=0, column=1, padx=5, pady=5)

tk.Label(root, text="Disk Requests (comma-separated):").grid(row=1, column=0, padx=5, pady=5, sticky="e")
request_entry = tk.Entry(root)
request_entry.grid(row=1, column=1, padx=5, pady=5)

# Algorithm Selection
tk.Label(root, text="Choose Algorithm:").grid(row=2, column=0, padx=5, pady=5, sticky="e")
algorithm_choice = ttk.Combobox(root, values=["AI-Based", "FCFS", "SSTF", "SCAN", "C-SCAN"])
algorithm_choice.grid(row=2, column=1, padx=5, pady=5)
algorithm_choice.set("AI-Based")  # Default selection

# Run Simulation Button
run_button = tk.Button(root, text="Run Simulation", command=run_simulation)
run_button.grid(row=3, column=0, columnspan=2, pady=10)

# Result Display
result_text = tk.Text(root, height=8, width=50)
result_text.grid(row=4, column=0, columnspan=2, padx=5, pady=5)

root.mainloop()
