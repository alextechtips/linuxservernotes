sftp username@ip.ip.ip.ip
-> enter password

// ls - list directory<br>
// lls - list local directory<br>
// pwd - present working directory<br>
// lpwd - local present working directory<br>
// lcd - local change directory<br>
// get filename.ext - get file from remote<br>
// get -r foldername - get a directory from remote<br>
// put filename.ext - put file into remote directory<br>
// rm filename.ext - remove file<br>
// bye/exit - quit<br>

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
