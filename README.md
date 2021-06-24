Переносим с /dev/sdg на /dev/sda
sgdisk /dev/sdg -R /dev/sda

Регенерировать uuid диска:

sgdisk -G /dev/sda

Понадобится для установки efi модулей:

yum install grub2-efi-modules

mkfs.vfat /dev/sda1
mkfs.ext4 /dev/sda2
mkswap /dev/sda3
mkfs.xfs /dev/sda4 - возможно придется поменять раздел на другой размер

Загружаемся из лайвсиди, можно взять clonezilla.

Потом перекопируем это всё в разделы целевые

Грузимся со старого диска.

Теперь нужно поставить efi загрузчик:

Зайти в chroot окружение нового диска

mount /dev/sda4 /mnt

mount /dev/sda2 /mnt/boot/

mount /dev/sda1 /mnt/boot/efi

Правим /etc/fstab на новые uuidы.

grub2-install –target=x86_64-efi –efi-directory=/mnt/boot/efi –bootloader-id=centos_new /dev/sda

mount –bind /dev /mnt/dev

chroot /mnt

mount -t proc proc /proc

mount -t sysfs sysfs /sys

И сгенерить новый гра-конфиг

grub2-mkconfig -o /boot/efi/EFI/centos/grub.cfg

Конфиг uefi менеджера:

efibootmgr -v

efibootmgr -c -d /dev/sda -p 7 -L <label> -l \EFI\<label>\grubx64.efi
