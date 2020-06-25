sftp username@ip.ip.ip.ip
-> enter password

// ls - list directory
// lls - list local directory
// pwd - present working directory
// lpwd - local present working directory
// lcd - local change directory
// get filename.ext - get file from remote
// get -r foldername - get a directory from remote
// put filename.ext - put file into remote directory
// rm filename.ext - remove file
// bye/exit - quit

Mount USB drive on WLS
sudo mkdir /mnt/e
sudo mount -t drvfs E: /mnt/g

Mount Network folder
sudo mkdir /mnt/folder
sudo mount -t '\\server\folder' /mnt/folder

RSYNC Backup foulders
rsync -azP localdir/ remotedir/

rsync -azP /mnt/d/My\ Data\ Folders/Pictures /mnt/e/Backup/

rsync -azP --delete /mnt/d/Video\ Editing /mnt/e/Backup/

rsync -azP /mnt/d/My\ Data\ Folders/Desktop /mnt/e/Backup/