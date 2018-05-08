import sys, os, time
import datetime
from subprocess import Popen, PIPE, list2cmdline
import argparse
import subprocess
def cpu_count():
    ''' Returns the number of CPUs in the system
    '''
    num = 1
    if sys.platform == 'win32':
        try:
            num = int(os.environ['NUMBER_OF_PROCESSORS'])
        except (ValueError, KeyError):
            pass
    elif sys.platform == 'darwin':
        try:
            num = int(os.popen('sysctl -n hw.ncpu').read())
        except ValueError:
            pass
    else:
        try:
            num = os.sysconf('SC_NPROCESSORS_ONLN')
        except (ValueError, OSError, AttributeError):
            pass

    return num
def done(p):
    return p.poll() is not None
def success(p):
    return p.returncode == 0
def fail():
        sys.exit(1)
def kt():
    currentTime = datetime.datetime.now()
    if (currentTime.isoweekday() in [1,2,3,4,5,7]) and currentTime.hour in range(17, 19):
        return 1
    else:return 0
def ck():
    if(kt()):fail()
ck()
k=0
max_task = 4
content=[]
with open('e.csv', 'r') as f:
    for l in f:
        content.append(l)
        if(len(content)>5000):break
cmds=content
processes = []
while True:    
    while cmds and len(processes) < max_task and not kt():
        task = cmds.pop()
        print(task)
        p = Popen(task, stdout=PIPE, stderr=PIPE,shell=True)
        processes.append(p)
    for p in processes:
        if done(p):
            if success(p):
                print(p.stdout.read())
                print(p.stderr.read())
                processes.remove(p)
            else:
                processes.remove(p)
    if(kt() and len(processes)<1):fail()
