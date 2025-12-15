# 🕵️ Sleuth Kit Usage in CTFs (Forensics)

This repository is a **CTF-focused practical guide** for using **The Sleuth Kit (TSK)** in **Digital Forensics challenges**.

The goal is not theory, but **exact commands and workflow** used in real Capture The Flag (CTF) competitions.

---

## 🧠 What is The Sleuth Kit?

**The Sleuth Kit (TSK)** is a collection of command-line tools used to analyze **disk images** and **file systems**.

In CTFs, it is mainly used to:

* Analyze `.img`, `.dd`, `.raw`, `.E01` files
* List files including **deleted files**
* Recover deleted data
* Analyze file metadata and timelines

---

## 🧪 Typical CTF Scenario

> You are given a disk image. The attacker deleted the flag. Recover it.

This guide follows the **exact workflow** to solve this type of challenge.

---

## 🔧 Core Sleuth Kit Tools

| Tool          | Usage in CTFs                  |
| ------------- | ------------------------------ |
| `mmls`        | Identify partitions            |
| `fsstat`      | Identify file system           |
| `fls`         | List files (including deleted) |
| `icat`        | Extract file content           |
| `istat`       | View inode metadata            |
| `tsk_recover` | Recover deleted files          |

---

## 🛠️ Step-by-Step Sleuth Kit Workflow

### 1️⃣ Identify Partitions

Always start here.

```bash
mmls disk.img
```

Example output:

```
Slot    Start        End
01:     2048         2099199
```

📌 Save the **Start sector (offset)** → `2048`

---

### 2️⃣ Identify File System

```bash
fsstat -o 2048 disk.img
```

This reveals:

* File system type (EXT4, NTFS, FAT)
* Block size
* Inode structure

---

### 3️⃣ List Files (Including Deleted)

List all files:

```bash
fls -o 2048 disk.img
```

Recursive listing:

```bash
fls -o 2048 -r disk.img
```

Show **deleted files only**:

```bash
fls -o 2048 -rd disk.img
```

Example output:

```
r/r * 24: flag.txt
```

`*` means **deleted file** (high-value target in CTFs).

---

### 4️⃣ Extract a Deleted File

Using the inode number (example: `24`):

```bash
icat -o 2048 disk.img 24
```

Save it to a file:

```bash
icat -o 2048 disk.img 24 > recovered_flag.txt
```

---

### 5️⃣ Inspect Metadata (Timestamps)

```bash
istat -o 2048 disk.img 24
```

Useful when the challenge asks:

* When was the file deleted?
* Which file was modified last?

---

### 6️⃣ Recover All Deleted Files

```bash
tsk_recover -o 2048 disk.img recovered/
```

Search for flags:

```bash
grep -R "CTF{" recovered/
strings recovered/* | grep FLAG
```

---

## ⏱️ Timeline Analysis (Advanced)

Create body file:

```bash
fls -o 2048 -r -m / disk.img > bodyfile.txt
```

Generate timeline:

```bash
mactime -b bodyfile.txt > timeline.txt
```

Used in challenges involving **activity reconstruction**.

---

## 🚩 Common CTF Tricks

* Flags hidden in deleted `.txt` files
* Flags inside `.bash_history`
* Flags hidden in recovered images (`strings` them)
* Multiple partitions (always run `mmls`)
* Fake flags (verify metadata)

---

## 🧾 Example Flag Format

```
flag{sl3uthk1t_ctf_m4st3r}
```

---

## 📌 Cheatsheet

```bash
mmls image.img
fsstat -o <offset> image.img
fls -o <offset> -rd image.img
icat -o <offset> image.img <inode>
istat -o <offset> image.img <inode>
tsk_recover -o <offset> image.img output/
```

---

## 🎯 Who Is This Repo For?

* CTF beginners in **Forensics**
* Students learning **Digital Forensics**
* Anyone who wants **practical Sleuth Kit usage**

---

## ⭐ License

Free to use for learning and CTFs.

---

Happy hunting 🕵️‍♂️

