# ripalot

ripalot is a tool for people who need to _rip a lot_ of audio CDs. Since it is written in Python,
its name can also be considered an homage to the musical Spamalot (which the developer has never
seen and has no particular interest in seeing, but thought the name similarity was worth
acknowledging anyway).

It's FreeBSD-only for now due to the use of OS-specific commands for interfacing with optical
drives. Linux support may or may not be added in the future.

## Dependencies
- cdparanoia
- cdda2wav
- flac
- Python 3.6

## Usage
IMPORTANT: Make sure to set your device permissions correctly (see the Gotchas selection below),
otherwise ripalot won't work.

Run ripalot from the command line, providing a list of disc names (one per line) on standard in.
ripalot will automatically detect and use all of the optical drives connected to your machine.
Whenever a drive tray opens, insert the next disc on the list and ripping will start automatically
once the tray is closed. Appropriately-named FLAC files for each disc will be created in the
directory that you ran the script from. That's really about it.

## Examples

- Compile a list file beforehand and pass it on stdin:

		echo "Some Album Disc 1" > disc_names.txt
		echo "Some Album Disc 2" >> disc_names.txt
		echo "Some Album Disc 3" >> disc_names.txt
		./ripalot < disc_names.txt

- Use echo for quick jobs:

		echo "Some Album Disc [3]" | ./ripalot

	(This is equivalent to the first example. The bracket syntax comes in handy for huge
	soundtracks and box sets.)

- Just run it...

		./ripalot

	...and input your disc names line by line at the terminal. You can continue adding names after
	ripping has started. ripalot will pause for more input whenever it runs out of names until EOF
	(Ctrl-D) is entered to signal the end of the list.

## Gotchas
- The user running ripalot has to have both high-level and low-level device access permissions.
Being a member of the `operator` group should be enough for high-level access, but low-level (pass
device) access also requires adding some lines to `/etc/devfs.conf` and rebooting:

		perm	xpt0	0660
		perm	pass0	0660
		perm	pass1	0660
		perm	pass2	0660

	The `pass#` lines should correspond to your optical drives, run `camcontrol devlist` as root to
	get the correct ones. These may change if you make changes to your hardware configuration,
	such as adding a controller card. I will try to make this entire process less painful with
	future updates.

- Amazingly enough, there are SATA controllers that will bottleneck two or more fast drives ripping
at full speed, causing their read speeds to fluctuate and possibly introducing read errors. This
seems absurd to me since even SATA I should have more than enough bandwidth, but switching the
drives to a different controller cleared up the issue when I ran into it so I'm not sure what else
it could be.
