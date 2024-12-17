# Step 1 again: Install `pi3d`
```pip install pi3d```
it timed out - why is it installing at 5kB/s? oh well, lets just give it a longer timer and try a mirror
```pip3 install pi3d --timeout 600 --index-url https://pypi.org/simple/```
it timed out still? ok maybe its just this one library its dependant on `pillow` ill just download this on my pc and put it on the sd card
-download and install old versions of each for arm64 linux-
```cd ../../boot```
```pip3 install Pillow-9.5.0-cp37-cp37m-manylinux_2_28_aarch64.whl```
Oh shit, this isn't arm64 is it
```uname -m```
this is just a 32bit system, and they haven't pre-compiled a .whl for linux 32x - so i HAVE to download it via the pip commands
It said `python-pip` could be updated, lets risk it
```python3 -m pip install --upgrade pip```
```pip3 install Pillow```
Wow that was a lot faster - now let get pi3d and be done with it
```pip3 install pi3d```
Holy shit it is freezing for like 10 minutes trying to install a library it is dependant on called `numpy`

# Step 3: Install `numpy`
Lets install `numpy` directly, maybe its the context
```pip3 install numpy```
This is still going really slow, lets open a new console and check performance

# Step 3 continued: Install `numpy`
```pip install pi3d```
I just waited an hour and it worked - small problem though - it was installed to the wrong path because i didn't give it permission to install it to the shared library path
```sudo install pi3d```
I waited another hour, then I restarted the raspberry pi and it failed because it couldnt import `board`

# Step 4: Install `Board`
it appears `board` is parth of `Adafruit-Blinka`, so i install that
```sudo pip install Adafruit-Blinka```
It takes a few seconds, i restart the raspberry pi and hope the imports are over
It fails because it needs `svg.path` - specifically the functions `Path` and `parse_path`

# Step 5: install `svg.path`
```sudo pip install svg.path```
the error is no longer because it cannot import `svg.path`, but rather because it cannot import the `path` function from `svg.path`
this error is unusual, im just going to uninstall and reinstall
```sudo pip uninstall svg.path```
```sudo pip install svg.path```
It is no longer failing to import things

# Step 6: The pickled data
On restart, an error occurs on line eyes.py, line 116 in <module>
At this point, im excited that i actually get to touch code, so i copy the code from the SD card and open it in VSCode
The error on line 116 that numpy cannot open a "pickled" file when trying to open iris.jpg because allow_pickle is set to false
I set up a windows samba server on the raspberry pi and made it share the SD card
```sudo apt install samba```
```sudo nano /etc/samba/smb.conf```
I add the following lines:
```[pi]```
```path = /```
```browseable = yes```
```writeable = yes```
```only guest = no```
```create mask = 0777```
```directory mask = 0777```
```public = yes```
I save, close and restart the samba service
```sudo systemctl restart smbd```
Now that i can access all the data on the raspberry pi, i try to allow_pickle on line 227 of pi3d/Texture.py to see if it helps
```sudo nano /usr/local/lib/python3.7/dist-packages/pi3d/Texture.py```
add "allow_pickle=True" to line 227, and restart the raspberry pi
It doesn't work

# Step 7: Doubts
The moment you start editing the code of libraries are have installed, it begins to dawn on you that this really is just supposed to work as is, and maybe something else is wrong
I reinvestigated the original install process, and i found that the installer is a python script that uses pip to install the libraries - so why were they failing?
Rather than remake the whole app, lets just do this all again, record the process and analyse why things went wrong

# Step 8: Do it all again
This time ill test it via HDMI to see if i can just get it working there
-20 minute recording of the installation process-
oh, it worked...

# Step 9: Make it work on the circuit
I reset the SDcard again, run the 20min installation process (recording it), and it works
I swear to god the only problem was that it needed an ethernet cord because it might have been timing out during downloads