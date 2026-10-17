# text-to-speech conversion

import tkinter as tk
from tkinter import messagebox, filedialog
import pyttsx3

engine = pyttsx3.init()

root = tk.Tk()
root.title("Text-to-Speech Desktop App")
root.geometry("650x520")
root.resizable(False, False)

voices = engine.getProperty("voices")

def speak():
    text = text_box.get("1.0", tk.END).strip()

    if not text:
        messagebox.showwarning("Warning", "Please enter some text.")
        return

    engine.setProperty("rate", speed.get())
    engine.setProperty("volume", volume.get() / 100)

    engine.say(text)
    engine.runAndWait()

def stop_speech():
    engine.stop()

def save_audio():
    text = text_box.get("1.0", tk.END).strip()

    if not text:
        messagebox.showwarning("Warning", "Please enter some text.")
        return

    path = filedialog.asksaveasfilename(
        defaultextension=".wav",
        filetypes=[("WAV audio", "*.wav")]
    )

    if path:
        try:
            engine.setProperty("rate", speed.get())
            engine.setProperty("volume", volume.get() / 100)
            engine.save_to_file(text, path)
            engine.runAndWait()
            messagebox.showinfo("Success", "Audio saved successfully.")
        except Exception as error:
            messagebox.showerror("Error", str(error))

def change_voice(event=None):
    index = voice_choice.current()
    if index >= 0:
        engine.setProperty("voice", voices[index].id)

tk.Label(
    root,
    text="Text-to-Speech Desktop App",
    font=("Arial", 18, "bold")
).pack(pady=12)

tk.Label(root, text="Enter your text:").pack()

text_box = tk.Text(root, height=10, width=68, wrap="word")
text_box.pack(padx=15, pady=8)

tk.Label(root, text="Select Voice:").pack()

voice_choice = tk.ttk.Combobox(
    root,
    state="readonly",
    width=45,
    values=[voice.name for voice in voices]
)
voice_choice.pack(pady=5)

if voices:
    voice_choice.current(0)
    change_voice()

voice_choice.bind("<<ComboboxSelected>>", change_voice)

tk.Label(root, text="Speech Speed:").pack()

speed = tk.Scale(
    root,
    from_=100,
    to=250,
    orient="horizontal",
    length=300
)
speed.set(175)
speed.pack()

tk.Label(root, text="Volume:").pack()

volume = tk.Scale(
    root,
    from_=0,
    to=100,
    orient="horizontal",
    length=300
)
volume.set(80)
volume.pack()

button_frame = tk.Frame(root)
button_frame.pack(pady=15)

tk.Button(
    button_frame, text="Speak",
    width=12, command=speak
).grid(row=0, column=0, padx=5)

tk.Button(
    button_frame, text="Stop",
    width=12, command=stop_speech
).grid(row=0, column=1, padx=5)

tk.Button(
    button_frame, text="Save Audio",
    width=12, command=save_audio
).grid(row=0, column=2, padx=5)

tk.Button(
    button_frame, text="Clear",
    width=12,
    command=lambda: text_box.delete("1.0", tk.END)
).grid(row=0, column=3, padx=5)

root.mainloop()

