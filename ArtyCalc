import matplotlib.pyplot as plt
import numpy as np
import tkinter as tk
from matplotlib.backends.backend_tkagg import FigureCanvasTkAgg
from tkinter import messagebox

# Constants
v = 40.45  # launch speed in m/s
g = 9.8    # gravity in m/s^2
angles_deg = np.arange(10, 71, 0.2)

# Initialize Tkinter window
root = tk.Tk()
root.title("Projectile Trajectory Calculator")
root.geometry("800x600")

# Frame for the Matplotlib canvas
frame = tk.Frame(root)
frame.pack(side=tk.TOP, fill=tk.BOTH, expand=True)

# Initial values for target
target_x = 167.0
target_y = 0.0

# Closest match tracking
closest_angle = None
min_distance = float('inf')
closest_x = None
closest_y = None

# Function to get trajectory arrays
def get_trajectory(theta_deg, num_points=500):
    theta_rad = np.radians(theta_deg)
    t_flight = 2 * v * np.sin(theta_rad) / g
    t = np.linspace(0, t_flight, num_points)
    x = v * np.cos(theta_rad) * t
    y = v * np.sin(theta_rad) * t - 0.5 * g * t**2
    return x, y

# Plotting function
def plot_trajectories():
    global closest_angle, closest_x, closest_y, min_distance
    closest_angle = None
    closest_x = None
    closest_y = None
    min_distance = float('inf')

    fig, ax = plt.subplots(figsize=(10, 6))
    ax.clear()

    # Precompute 70° trajectory
    x_70, y_70 = get_trajectory(70)

    for theta_deg in angles_deg:
        x, y = get_trajectory(theta_deg)

        # Truncate below 70° arc for angles < 45
        if theta_deg < 45 and not np.isclose(theta_deg, 45.0):
            y_70_interp = np.interp(x, x_70, y_70)
            mask = y <= y_70_interp
            x = x[mask]
            y = y[mask]
            if len(x) == 0:
                continue  # skip if everything is clipped

        # Calculate distance to target
        distances = np.sqrt((x - target_x) ** 2 + (y - target_y) ** 2)
        if len(distances) == 0:
            continue
        closest_idx = np.argmin(distances)
        closest_dist = distances[closest_idx]

        # Ignore clipped arcs for closest match
        is_clipped = theta_deg < 45 and len(x) < 500
        if not is_clipped and closest_dist < min_distance:
            min_distance = closest_dist
            closest_angle = theta_deg
            closest_x = x
            closest_y = y

        ax.plot(x, y, color='SteelBlue', linewidth=0.3)

    # Highlight closest
    if closest_x is not None and closest_y is not None:
        ax.plot(closest_x, closest_y, label=f"Closest: {round(closest_angle, 2)}°", color='black', linewidth=2)

    ax.plot(target_x, target_y, 'ro', label="Target")

    ax.set_title("Projectile Trajectories (10° to 70°)")
    ax.set_xlabel("Horizontal Distance (m)")
    ax.set_ylabel("Vertical Height (m)")
    ax.grid(True)
    ax.axhline(0, color='gray', linewidth=0.5)
    ax.axvline(167, color='red', linestyle='--', label="Target at 167m")

    ax.text(
        0.5 * ax.get_xlim()[1],
        0.5 * ax.get_ylim()[1],
        f"Closest Angle: {round(closest_angle, 2)}°" if closest_angle else "No Match",
        fontsize=14,
        color='black',
        ha='center',
        va='center',
        bbox=dict(facecolor='white', alpha=0.7, edgecolor='black')
    )

    for widget in frame.winfo_children():
        widget.destroy()

    canvas = FigureCanvasTkAgg(fig, master=frame)
    canvas.draw()
    canvas.get_tk_widget().pack(side=tk.TOP, fill=tk.BOTH, expand=True)

# Entry + button handlers
def on_calculate():
    global target_x, target_y
    try:
        target_x = float(entry_x.get())
        target_y = float(entry_y.get())
        if target_x < 0 or target_y < 0:
            raise ValueError("Target distances must be non-negative.")
        plot_trajectories()
    except ValueError as e:
        messagebox.showerror("Invalid Input", str(e))

# GUI widgets
label_x = tk.Label(root, text="Enter Horizontal Distance (m):")
label_x.pack(pady=5)
entry_x = tk.Entry(root)
entry_x.insert(0, "167.0")
entry_x.pack(pady=5)

label_y = tk.Label(root, text="Enter Vertical Distance (m):")
label_y.pack(pady=5)
entry_y = tk.Entry(root)
entry_y.insert(0, "0.0")
entry_y.pack(pady=5)

calc_button = tk.Button(root, text="Calculate", command=on_calculate)
calc_button.pack(pady=20)

plot_trajectories()
root.mainloop()
