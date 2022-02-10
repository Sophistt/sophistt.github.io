---
title: Differences in Serveral Executable Commands in Linux
date: 2019-07-31 13:59:55
tags: bash
categories: Linux
---

In Linux, there are several commands to execute shell scripts such as `bash`, `source`, `sh`, `./`. So, what are the differences of them?

# `source`

```bash
source a.sh
```
Execute the specified shell script in current bash, and the **executable** authority of this script is unneccesary. Besides, `.` equals `source`:

```bash
. a.sh
```

# `bash/sh`

```bash
sh a.sh
bash a.sh
```
Execute the specified shell script in the **subshell**, and the **executable** authority of this script is also unneccesary.

# `./`

```bash
./ a.sh
```

Execute the specified shell script in the **subshell**, and the **executable** authority of this script is also **neccesary** so that `chmod u+x .` can be used to 
change the authority of the scirpt.

