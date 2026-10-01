# Python-Automation-Utility
Using pathlib,CSV,shutil libraries for managing folders

from pathlib import Path
import shutil
import csv

# Folder to organize
DOWNLOADS = Path("Downloads")

# File categories
FILE_TYPES = {
    "PDFs": [".pdf"],
    "Images": [".jpg", ".jpeg", ".png", ".gif"],
    "Videos": [".mp4", ".mkv", ".avi"],
    "Documents": [".docx", ".doc", ".txt"],
    "Excel": [".xlsx", ".xls", ".csv"],
    "Archives": [".zip", ".rar", ".7z"]
}

report_data = []

for file in DOWNLOADS.iterdir():

    if file.is_dir():
        continue

    moved = False

    for folder, extensions in FILE_TYPES.items():

        if file.suffix.lower() in extensions:

            destination = DOWNLOADS / folder
            destination.mkdir(exist_ok=True)

            new_location = destination / file.name

            try:
                shutil.move(str(file), str(new_location))

                report_data.append(
                    [file.name, folder]
                )

                print(
                    f"Moved {file.name} -> {folder}"
                )

            except Exception as e:
                print(
                    f"Error moving {file.name}: {e}"
                )

            moved = True
            break

    if not moved:

        other_folder = DOWNLOADS / "Others"
        other_folder.mkdir(exist_ok=True)

        shutil.move(
            str(file),
            str(other_folder / file.name)
        )

        report_data.append(
            [file.name, "Others"]
        )

# Create CSV report
report_file = DOWNLOADS / "report.csv"

with open(
    report_file,
    "w",
    newline="",
    encoding="utf-8"
) as f:

    writer = csv.writer(f)

    writer.writerow(
        ["Filename", "Category"]
    )

    writer.writerows(report_data)

print("\nReport generated successfully!")
