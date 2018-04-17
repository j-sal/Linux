

*******************************************************************************
*									      *
*			Jogebeth Gomez Salazar				      *
*			Booga driver for Linux				      *
*									      *
*******************************************************************************



Observations: This project is focused on providing a simple driver with the 
functionality of reading a string of character and writing an output of characters
randomly generated with the help of linux/random.h

The test program test-booga.c covers testing for both functions.

The basic script plan.sh has the functionality of running said test program.

To run plan.sh:
./plan.sh

A plan for fully testing the driver would be as follows: 

Testing Checklist
1) Functionality Testing
2) Usability testing
3) Performance testing

Activities to be covered:

1) Functionality Testing
	Invoke the driver from different processes to test the semaphore conf
	kill a process which is currently using the driver
	Try to access an unconfigured driver device, i.e /dev/booga5
		test-booga <minor number> <buffersize> <read|write>
		test-booga 5 100 write
	Input a buffersize bigger than allowed

2) Usability testing
	Test for ease of access and use 

3) Performance testing
	Load testing to see if semaphores work well under strain
	Stress testing to see if configured parameters of buffer 
		and assigned devices are enough for most situations
	Test for ease of access and use after resets


Other information:

To load the driver:

#!/bin/sh 
module="booga"
device="booga"
group="root"
mode="644"

# invoke insmod with all arguments 
if test -f "$module.ko"
then
	/sbin/insmod  $module.ko $* || exit 1
else
	/sbin/insmod  $module.o $* || exit 1
fi

major=`cat /proc/devices | awk "\\$2==\"$module\" {print \\$1}"`

# Remove stale nodes and replace them, then give gid and perms

rm -f /dev/${device}[0-3]
mknod /dev/${device}0 c $major 0
mknod /dev/${device}1 c $major 1
mknod /dev/${device}2 c $major 2
mknod /dev/${device}3 c $major 3
ln -sf ${device}0 /dev/${device}
chgrp $group /dev/${device}[0-3] 
chmod $mode  /dev/${device}[0-3]


To unload the driver:

#!/bin/sh

module="booga"
device="booga"

/sbin/rmmod $module $* || exit 1

rm -f /dev/${device}[0-3] /dev/${device}