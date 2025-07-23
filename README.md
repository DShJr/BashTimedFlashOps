# BashTimedFlashOps

## EN

### Description
This project features a set of **Bash scripts** designed for automated file management on a USB flash drive. The primary goal is to prevent the drive from becoming full and to streamline the process of deleting outdated files.

**The repository contains two main scripts:**

* One script: simple_generate_files.sh - **creates Bash files** on the flash drive at a specified time using the **`cron` scheduler**.
* The other script: simple_cleanup_files.sh - **deletes these files**, also on a `cron` schedule, eliminating the need for manual cleanup.

This is a simplified version of a homework assignment, showcasing basic skills in working with Bash scripts and `cron` for automating routine tasks.

### Assignment Execution Process

  This section details the setup and scripts used for the assignment.

  **We will use:**
1. **Flash Drive Path:**
   `/media/user/SMARTBUY`
   `SMARTBUY` - is the name of my flash drive.
   
3. **Files Folder:**
  `test_files_generation`
   This folder will be created on the flash drive.
   The script will create files for deletion in this folder, and another script will delete them. All operations will be scheduled using `cron`.
   
5. **File Name Prefix:**
   `dummy_file_`
   This will be the naming pattern for the files.


  **Bash Script #1: File Generation**

**Purpose:** File creation only.

**Script Name:** `simple_generation_files.sh`

**Script Description:** This script will simply create a specified number of files (in this case, 100), each sized at 1MB, without checking disk fill percentage.

**Script Code:**

```bash
#!/bin/bash

# --- SETTINGS ---
flash_drive_path="/media/user/SMARTBUY"
files_folder="test_files_generation"
full_path_to_folder="$flash_drive_path/$files_folder"

file_size_bytes=1048576 # 1MB
filename_prefix="dummy_file_"
number_of_files_to_create=100
# --- /SETTINGS ---

# --- Create the folder if it doesn't exist ---
mkdir -p "$full_path_to_folder" || {
  echo "Error: Could not create folder $full_path_to_folder. Check permissions or flash drive satus."
  exit 1
}

# --- Change directory to the created folder ---
cd "$full_path_to_folder" || {
echo "Error: Could not change directory to $full_path_to_folder. Check path or folder existence."
exit 1
}

echo "Starting to create $number_of_files_to_create dummy files in $full_path_to_folder..."

for i in $(seq 1 $full_path_to_folder) # Corrected loop variable
do
  current_filename="${filename_prefix}${i}.bin"
  dd if=/dev/zero of="$current_filename" bs="$file_size_bytes" count=1 status=none
  if [ $? -ne 0 ]
  then
    echo "Error: creating file $current_filename. Stopping."
    exit 1
  fi
  echo "Created file: $current_filename"
  sleep 0.1 # Small delay to avoid overwhelming the system/flash drive
done

echo "Finished creating files."
```

  **Bash Script #2: Files Deleted**

**Purpose:** File deletion only.

**Script Name:** `simple_cleanup_files.sh`

**Script Description:** This script will simply delete all files with the `dummy_file_` prefix from the specified folder, without checking free space percentage.

**Script Code:**

```bash
#!/bin/bash

# --- SETTINGS ---
flash_drive_path="/media/user/SMARTBUY"
files_folder="test_files_generation"
full_path_to_folder="$flash_drive_path/$files_folder"

filename_pattern="dummy_file_*.bin"
# --- /SETTINGS --

# --- Check if the folder exists ---
if [ ! -d "$full_path_to_folder" ]
  then
    echo "Error: Folder $full_path_to_folder not found. Deletion skipped."
    exit 1
fi

echo "Starting to delete files matching '$filename_pattern' from '$full_path_to_folder'"

# --- Find and Delete files ----
find "$full_path_to_folder" -type f -name "$filename_pattern" -delete

if [ $? -eq 0 ]
then
  echo "Successfully deleted files from folder: $full_path_to_folder"
else
  echo "Errors occured during file deletion from: $full_path_to_folder"
fi
```

  **Setting Permissions for Scripts #1 and #2**
  
  To make the scripts executable:
  `simple_generation_files.sh`
  `simple_cleanup_files.sh`

```bash
chmod +x simple_generation_files.sh
chmod +x simple_cleanup_files.sh
```

  **Scheduling Automatic Script Execution with `Cron`**
To automate the execution of these scripts:

1. **Open the cron editor:**
   crontab -e
   
2. **Add the tasks to `crontab`:**

2.1 **For the file creation script** (simple_generation_files.sh)

   **Task:** It should run at the beginning of every hour.
   
   **Execution:**
   0**** /home/user/..../simple_generation_files.sh >> /tmp/generate_log.txt 2>&1

2.2 **For the file cleanup script** (simple_cleanup_files.sh)

   **Task:** It should run 10 minutes past the beginning of every hour.
   
   **Execution:**
   10**** /home/user/..../simple_cleanup_files.sh >> /tmp/generate_log.txt 2>&1


---
## RU

### Описание
Этот проект представляет собой набор Bash-скриптов для автоматического управления файлами на флешке. Основная цель — предотвратить переполнение накопителя и упростить процесс удаления устаревших файлов.

**В репозитории представлены два основных скрипта:**

* Один скрипт: simple_generate_files.sh - **создаёт** Bash-файлы на флешке в заданное время с помощью планировщика `cron`.
* Другой скрипт: simple_cleanup_files.sh - **удаляет** эти файлы, также по расписанию `cron`, что избавляет от необходимости ручной очистки.

Это упрощённая версия домашнего задания, демонстрирующая базовые навыки работы с Bash-скриптами и `cron` для автоматизации рутинных задач.

### Процесс выполнение задания

  **Будем использовать:**
1. **Путь к флэшке:**
   `/media/user/SMARTBUY`
   SMARTBUY - имя моей флэшки.
2. **Папка для файлов:**
  `test_files_generation`
   Эта папка будет создана на этой флэшке.
   В ней скрипт будет создавать файлы для удаления. А другой скрипт юудет их удалять. И всё по расписанию в `cron`.
4. **Префикс имени файла:**
   `dummy_file_`
   Шаблон названия имён файлов.


  **Скрипт Bash #1.**

**Назначение:** Только создание файлов.
**Имя скрипта:** `simple_generation_files.sh`
**Описание скрипта:** Он будет просто создавать определённое количество файлов (в нашем случае, 100), каждый размером в 1МБ, не проверяя процент заполнения диска.

**Код скрипта:**

```bash
#!/bin/bash

# --- SETTINGS ---
flash_drive_path="/media/user/SMARTBUY"
files_folder="test_files_generation"
full_path_to_folder="$flash_drive_path/$files_folder"

file_size_bytes=1048576
filename_prefix="dummy_file_"
number_of_files_to_create=100
# --- /SETTINGS ---

# --- Create the folder if it doesn't exist ---
mkdir -p "$full_path_to_folder" || {
  echo "Ошибка: Не удалось создать папку $full_path_to_folder. Проверьте права доступа или состояние флешки."
  exit 1
}

# --- Change directory to the created folder ---
cd "$full_path_to_folder" || {
echo "Ошибка: Не удалось перейти в директорию $full_path_to_folder. Проверьте путь или существование папки."
exit 1
}

echo "Начинается создание $number_of_files_to_create тестовых файлов в $full_path_to_folder..."

for i in $(seq 1 $full_path_to_folder)
do
  current_filename="${filename_prefix}${i}.bin"
  dd if=/dev/zero of="$current_filename" bs="$file_size_bytes" count=1 status=none
  if [ $? -ne 0 ]
  then
    echo "Ошибка: создание файла $current_filename. Остановка."
    exit 1
  fi
  echo "Создан файл: $current_filename"
  sleep 0.1
done

echo "Завершено создание файлов."
```

  **Скрипт Bash #2.**

**Назначение:** Только для удаления файлов.
**Имя скрипта:** `simple_cleanup_files.sh`
**Описание скрипта:** Он будет просто удалять все файлы с префиксом `dummy_file_` из указанной папки, не проверяя процент свободного места.

**Код скрипта:**

```bash
#!/bin/bash

# --- SETTINGS ---
flash_drive_path="/media/user/SMARTBUY"
files_folder="test_files_generation"
full_path_to_folder="$flash_drive_path/$files_folder"

filename_pattern="dummy_file_*.bin"
# --- /SETTINGS --

# --- Check if the folder exists ---
if [ ! -d "$full_path_to_folder" ]
  then
    echo "Ошибка: Папка $full_path_to_folder не найдена. Удаление пропущено."
    exit 1
fi

echo "Начинается удаление файлов, соответствующих шаблону '$filename_pattern' из '$full_path_to_folder'"

# --- Find and Delete files ----
find "$full_path_to_folder" -type f -name "$filename_pattern" -delete

if [ $? -eq 0 ]
then
  echo "Файлы успешно удалены из папки: $full_path_to_folder"
else
  echo "Произошли ошибки во время удаления файлов из: $full_path_to_folder"
fi
```

  **Назначим права для скриптов #1 и #2**
  `simple_generation_files.sh`
  `simple_cleanup_files.sh`

```bash
chmod +x simple_generation_files.sh
chmod +x simple_cleanup_files.sh
```

  **Настройка Автоматического запуска этих скриптов по расписанию с помощью `cron`**

1. **Открываем редактор `cron`**
   crontab -e

2. **Добавим задачи в `crontab`**

2.1 **Для скрипта создания файлов** (simple_generation_files.sh)
   **Задача:** Он должен запускаться в начале каждого часа.
   **Выполнение:**
   0**** /home/user/..../simple_generation_files.sh >> /tmp/generate_log.txt 2>&1

2.2 **Для скрипта удаления файлов (simple_cleanup_files.sh)
   **Задача:** Он должен запускаться каждые 10 минут каждого часа.
  **Выполнение:**
   10**** /home/user/..../simple_cleanup_files.sh >> /tmp/generate_log.txt 2>&1
