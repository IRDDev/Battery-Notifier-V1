import tkinter as tk
from tkinter import messagebox
import psutil
import winsound
import threading
import time
import pystray
from PIL import Image, ImageDraw


# ============================================================
# CONFIGURATION
# ============================================================

DEFAULT_FULL_BATTERY = 100
DEFAULT_LOW_BATTERY = 20

TARGET_FULL = DEFAULT_FULL_BATTERY
TARGET_LOW = DEFAULT_LOW_BATTERY

is_running = True

full_notified = False
low_notified = False

root = None
tray_icon = None
entry_full = None
entry_low = None


# ============================================================
# BATTERY MONITOR
# ============================================================

def monitor_battery():
    global full_notified, low_notified

    while is_running:
        try:
            battery = psutil.sensors_battery()

            if battery is not None:
                percent = int(battery.percent)
                plugged = battery.power_plugged

                # ------------------------------------------------
                # FULL BATTERY
                # ------------------------------------------------

                if plugged and percent >= TARGET_FULL:
                    if not full_notified:
                        winsound.Beep(1000, 500)
                        full_notified = True
                else:
                    if percent < TARGET_FULL:
                        full_notified = False

                # ------------------------------------------------
                # LOW BATTERY
                # ------------------------------------------------

                if not plugged and percent <= TARGET_LOW:
                    if not low_notified:
                        winsound.Beep(700, 500)
                        low_notified = True
                else:
                    if percent > TARGET_LOW:
                        low_notified = False

        except Exception:
            pass

        time.sleep(3)


# ============================================================
# SAVE SETTINGS
# ============================================================

def save_settings():
    global TARGET_FULL, TARGET_LOW
    global full_notified, low_notified

    try:
        full_value = int(entry_full.get())
        low_value = int(entry_low.get())

        if not 1 <= full_value <= 100:
            raise ValueError

        if not 0 <= low_value <= 100:
            raise ValueError

        if low_value >= full_value:
            messagebox.showerror(
                "Error",
                "Low Battery harus lebih kecil dari Full Battery.",
                parent=root
            )
            return

        TARGET_FULL = full_value
        TARGET_LOW = low_value

        # Reset status notifikasi agar batas baru
        # dapat diterapkan pada siklus berikutnya.
        full_notified = False
        low_notified = False

        messagebox.showinfo(
            "Berhasil",
            "Pengaturan baterai berhasil disimpan.",
            parent=root
        )

    except ValueError:
        messagebox.showerror(
            "Error",
            "Masukkan angka yang valid.\n"
            "Full Battery: 1-100\n"
            "Low Battery: 0-100",
            parent=root
        )


# ============================================================
# SYSTEM TRAY ICON
# ============================================================

def create_tray_image():
    image = Image.new("RGBA", (64, 64), (0, 0, 0, 0))
    draw = ImageDraw.Draw(image)

    # Body baterai
    draw.rounded_rectangle(
        (10, 16, 54, 56),
        radius=6,
        fill="#202020",
        outline="#FFFFFF",
        width=3
    )

    # Terminal baterai
    draw.rectangle(
        (24, 9, 40, 16),
        fill="#FFFFFF"
    )

    # Isi baterai
    draw.rectangle(
        (16, 22, 48, 50),
        fill="#38C172"
    )

    return image


def open_settings(icon=None, item=None):
    if root is not None:
        root.after(0, show_settings)


def show_settings():
    if root is None:
        return

    try:
        root.deiconify()
        root.lift()
        root.focus_force()
    except tk.TclError:
        pass


def hide_settings():
    if root is not None:
        root.withdraw()


def quit_application(icon=None, item=None):
    global is_running

    is_running = False

    if tray_icon is not None:
        tray_icon.stop()

    if root is not None:
        root.after(0, root.destroy)


def setup_tray():
    global tray_icon

    menu = pystray.Menu(
        pystray.MenuItem(
            "Open Settings",
            open_settings,
            default=True
        ),
        pystray.MenuItem(
            "Exit",
            quit_application
        )
    )

    tray_icon = pystray.Icon(
        "BatteryNotifierV1",
        create_tray_image(),
        "Battery Notifier V1",
        menu
    )

    tray_icon.run()


# ============================================================
# APPLICATION
# ============================================================

def setup_gui():
    global root, entry_full, entry_low

    root = tk.Tk()

    root.title("Battery Notifier V1")
    root.geometry("300x225")
    root.resizable(False, False)

    # --------------------------------------------------------
    # FULL BATTERY
    # --------------------------------------------------------

    tk.Label(
        root,
        text="Full Battery (%)"
    ).pack(pady=(15, 3))

    entry_full = tk.Entry(
        root,
        width=10,
        justify="center"
    )

    entry_full.pack()
    entry_full.insert(0, str(DEFAULT_FULL_BATTERY))

    # --------------------------------------------------------
    # LOW BATTERY
    # --------------------------------------------------------

    tk.Label(
        root,
        text="Low Battery (%)"
    ).pack(pady=(10, 3))

    entry_low = tk.Entry(
        root,
        width=10,
        justify="center"
    )

    entry_low.pack()
    entry_low.insert(0, str(DEFAULT_LOW_BATTERY))

    # --------------------------------------------------------
    # SAVE SETTINGS
    # --------------------------------------------------------

    tk.Button(
        root,
        text="Save Settings",
        command=save_settings
    ).pack(pady=12)

    # Tutup jendela = sembunyikan ke system tray.
    root.protocol("WM_DELETE_WINDOW", hide_settings)

    # --------------------------------------------------------
    # START BACKGROUND TASKS
    # --------------------------------------------------------

    threading.Thread(
        target=monitor_battery,
        daemon=True
    ).start()

    threading.Thread(
        target=setup_tray,
        daemon=True
    ).start()

    root.mainloop()


# ============================================================
# ENTRY POINT
# ============================================================

if __name__ == "__main__":
    setup_gui()
