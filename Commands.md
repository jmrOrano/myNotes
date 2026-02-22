#### The lsblk command
Sample output: 
sdc      8:32   1   3.8G  0 disk 
└─sdc1   8:33   1   3.7G  0 part /media/linuxuser/F4FD-93EE

|                             | Desc                           |
| :-------------------------: | ------------------------------ |
|   sdc 8:32 1 3.8G 0 disk    | - Ito yung mismong USB Drive   |
| \|-sdc1 8:833 1 3.7G 0 part | - ito yung partition           |
|      /media/linuxuser/      | -Dito naka mount ang partition |
|          F4FD-93EE          | -Ito yung name ng USB          |

	**PANO SAFELY EJECT AND USB DRIVE? **
		unmount muna ang partition
				sudo umount /dev/sdc1
		then power off the device
					sudo udisksctl power-off -b /dev/sdc

|                                       | col2                                                                                                              |
| :-----------------------------------: | ------------------------------------------------------------------------------------------------------ |
|                  -b                   | -- block-device : ang ibibigay mong argument                                                                      |
| bakit  sa linux, lahat ng hardware devices ay ***nirerepresent bilang files*** sa loob ng directory na : /dev es  es  es  es  es  es  es  es  es  es  es  |


----

