# CodeAlpha_Task4
import os
import shutil
import tkinter as tk
from tkinter import filedialog
import logging
from collections import defaultdict

# Setup logging
logging.basicConfig(
    filename="file_organizer.log",
    level=logging.INFO,
    format="%(asctime)s - %(levelname)s - %(message)s"
)

# Define file type categories
FILE_TYPES = {
    "Documents": [".pdf", ".docx", ".doc", ".txt", ".xlsx", ".csv", ".pptx"],
    "Images": [".jpg", ".jpeg", ".png", ".gif", ".bmp", ".svg"],
    "Videos": [".mp4", ".mkv", ".avi", ".mov", ".flv"],
    "Music": [".mp3", ".wav", ".aac", ".flac"],
    "Archives": [".zip", ".rar", ".tar", ".gz", ".7z"],
    "Scripts": [".py", ".js", ".sh", ".bat", ".html", ".css"],
    "Others": []
}

# Organize files in a directory
def organize_files_recursively(folder_path):
    if not os.path.exists(folder_path):
        print(f"The folder '{folder_path}' does not exist.")
        return

    for root, dirs, files in os.walk(folder_path):
        for file_name in files:
            file_path = os.path.join(root, file_name)
            file_extension = os.path.splitext(file_name)[1].lower()
            moved = False

            for category, extensions in FILE_TYPES.items():
                if file_extension in extensions:
                    category_folder = os.path.join(folder_path, category)
                    os.makedirs(category_folder, exist_ok=True)
                    shutil.move(file_path, os.path.join(category_folder, file_name))
                    logging.info(f"Moved '{file_name}' from '{root}' to '{category_folder}'.")
                    moved = True
                    break

            if not moved:
                others_folder = os.path.join(folder_path, "Others")
                os.makedirs(others_folder, exist_ok=True)
                shutil.move(file_path, os.path.join(others_folder, file_name))
                logging.info(f"Moved '{file_name}' from '{root}' to 'Others' folder.")

    print("Files and subdirectories have been organized successfully!")

# Generate a summary report
def generate_summary(folder_path):
    summary = defaultdict(int)
    for category in FILE_TYPES.keys():
        category_folder = os.path.join(folder_path, category)
        if os.path.exists(category_folder):
            summary[category] = len(os.listdir(category_folder))

    print("\nSummary Report:")
    for category, count in summary.items():
        print(f"{category}: {count} files")

# GUI to select folder
def select_folder():
    folder_path = filedialog.askdirectory()
    if folder_path:
        organize_files_recursively(folder_path)
        generate_summary(folder_path)

# Create GUI
def create_gui():
    root = tk.Tk()
    root.title("File Organizer")

    label = tk.Label(root, text="Select a folder to organize files:", font=("Arial", 14))
    label.pack(pady=10)

    button = tk.Button(root, text="Browse", command=select_folder, font=("Arial", 12), bg="blue", fg="white")
    button.pack(pady=20)

    root.mainloop()

# Main entry point
if __name__ == "__main__":
    create_gui()


def organize_files_with_logging(folder_path):
    if not os.path.exists(folder_path):
        logging.error(f"The folder '{folder_path}' does not exist.")
        return

    for file_name in os.listdir(folder_path):
        file_path = os.path.join(folder_path, file_name)

        if os.path.isdir(file_path):
            continue

        file_extension = os.path.splitext(file_name)[1].lower()
        moved = False

        for category, extensions in FILE_TYPES.items():
            if file_extension in extensions:
                category_folder = os.path.join(folder_path, category)
                os.makedirs(category_folder, exist_ok=True)
                shutil.move(file_path, os.path.join(category_folder, file_name))
                logging.info(f"Moved '{file_name}' to '{category_folder}'.")
                moved = True
                break

        if not moved:
            others_folder = os.path.join(folder_path, "Others")
            os.makedirs(others_folder, exist_ok=True)
            shutil.move(file_path, os.path.join(others_folder, file_name))
            logging.warning(f"File '{file_name}' moved to 'Others' folder.")

    print("Files have been organized successfully!")
