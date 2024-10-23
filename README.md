# **Forensic File Recovery and Imaging Using Scalpel on Kali Linux**

This project demonstrates the process of using **Scalpel**, a file carving tool, to recover a deleted file from a disk and create a forensic image of the recovered data. We focus on recovering a specific file (`Readme.md`) that was deleted and documenting the steps for forensically sound data recovery.

## **Project Overview**
The goal of this project is to:
1. Recover a deleted file (`Readme.md`) using **Scalpel**.
2. Create a forensic disk image containing the recovered files.
3. Generate a hash of the forensic image to ensure data integrity.

This document explains the step-by-step process, tools used, and reasoning behind each step in the recovery process.

---

## **Tools Used**
- **Kali Linux**: A Linux distribution designed for digital forensics and penetration testing.
- **Scalpel**: A file carving tool that recovers deleted files based on file headers and footers.
- **dd**: A utility to create a forensic disk image.
- **md5sum/sha256sum**: Tools to generate a hash of the forensic image to verify its integrity.

---

## **Process Breakdown**

### **1. Setup the Environment**

#### **Step**: Install Scalpel
To begin, we need to ensure that **Scalpel** is installed on the system.

```bash
sudo apt update
sudo apt install scalpel
```

#### **Reason**:
Scalpel is the primary tool used to recover deleted files. It works by scanning the disk or partition for specific file types based on headers/footers or other identifiable patterns.

### **2. Create a Directory for Recovered Files**

#### **Step**: Create a directory to store the recovered files.

```bash
mkdir ~/DFID/recoverFiles
```

#### **Reason**:
We need a location where Scalpel will store the files it recovers during the carving process. This keeps the recovered data organized and isolated from the rest of the system.

### **3. Identify the Partition**

#### **Step**: Identify the partition where the deleted file was located.

```bash
df -h
lsblk
```

#### **Reason**:
We need to specify which partition or storage device held the deleted file so that Scalpel knows where to scan for the data.

### **4. Configure Scalpel for File Recovery**

#### **Step**: Edit Scalpel’s configuration file (`scalpel.conf`).

```bash
sudo nano /etc/scalpel/scalpel.conf
```

Uncomment or add the following line to recover text-based files (like `Readme.md`):

```bash
txt     y   5000000       \x20\x20\x00\x00\x00\x00\x00\x00       \x20\x20\x20\x20
```

Save and exit.

#### **Reason**:
Scalpel works by searching for specific file types based on the patterns defined in its configuration file. Since `Readme.md` is a text-based file, we modify the configuration to include text files in the recovery process.

### **5. Run Scalpel to Recover the Deleted File**

#### **Step**: Run Scalpel on the identified partition.

```bash
sudo scalpel /dev/sda1 -o ~/DFID/recoverFiles
```

#### **Reason**:
We use this command to run Scalpel on the specified partition (`/dev/sda1` in this case) and output any recovered files to the `~/DFID/recovered_files` directory. Scalpel will analyze the entire partition to locate any files that match the specified criteria (text files in this case).

### **6. Verify the Recovered Files**

#### **Step**: After Scalpel completes the recovery process, verify that the deleted `Readme.md` file has been recovered.

```bash
cd ~/DFID/recoverFiles
grep -r "Readme" ~/DFID/recoverFiles
```

#### **Reason**:
This step allows us to confirm that Scalpel successfully recovered the deleted `Readme.md` file. We use `grep` to search through the recovered files for any instances of the term "Readme."

### **7. Create a Forensic Image of the Recovered Files**

#### **Step**: Use the `dd` command to create a forensic image of the recovered files.

```bash
sudo dd if=/dev/zero of=~/DFID/recoverFiles.img bs=1M count=1000
```

Mount the newly created image:

```bash
sudo mount -o loop ~/DFID/recoverFiles.img /mnt
```

Copy the recovered files into the image:

```bash
sudo cp -r ~/DFID/recoverFiles/* /mnt
sudo umount /mnt
```

#### **Reason**:
The `dd` command is commonly used in digital forensics to create a bit-for-bit copy (disk image) of the recovered files. This ensures that the recovered data is preserved in a forensically sound manner. By copying the files into a forensic image, we protect the integrity of the recovered data for further analysis.

### **8. Generate a Hash for the Forensic Image**

#### **Step**: Generate a hash of the forensic image to ensure integrity.

- **For MD5 hash**:
  ```bash
  md5sum ~/DFID/recoverFiles.img > ~/DFID/recoverFiles.img.md5
  ```

- **For SHA-256 hash**:
  ```bash
  sha256sum ~/DFID/recoverFiles.img > ~/DFID/recoverFiles.img.sha256
  ```

#### **Reason**:
Generating a hash of the forensic image allows us to verify that the image has not been altered or tampered with in the future. Hashes are critical in forensic investigations to confirm the authenticity and integrity of evidence.

### **9. Verify the Hash Value**

#### **Step**: Verify the generated hash values to ensure the image's integrity.

```bash
cat ~/DFID/recoverFiles.img.md5
cat ~/DFID/recoverFiles.img.sha256
```

#### **Reason**:
This final step checks the generated hash values and ensures that they can be used to validate the image in future forensic analyses.

---

## **Conclusion**

This project demonstrates a step-by-step guide for recovering a deleted file (`Readme.md`) using Scalpel and ensuring the integrity of the recovery process through forensic imaging and hashing. Scalpel is a powerful tool for file carving, and using it in conjunction with tools like `dd` and `md5sum`/`sha256sum` ensures a thorough and sound forensic workflow.

---

## **References**

- [Scalpel Documentation](https://www.sleuthkit.org/sleuthkit/docs/scalpel.html)
- [Kali Linux Official Site](https://www.kali.org/)
