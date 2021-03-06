---
layout: post
title: Miscelleneous 
date: 2020-09-10 00:00:00 +0300
description: You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. # Add post description (optional)
img: js-1.png # Add image post (optional)
tags: [Js, Conference] # add tag
---
MySQL Dump:
```
docker exec some-mysql sh -c 'exec mysqldump --all-databases -uroot -p"$MYSQL_ROOT_PASSWORD"' > /some/path/on/your/host/all-databases.sql
```
MySQL Restore of the Dump
```
docker exec -i some-mysql sh -c 'exec mysql -uroot -p"$MYSQL_ROOT_PASSWORD"' < /some/path/on/your/host/all-databases.sql
```

How to setup VSCode Remote:
```commit_id=cd9ea6488829f560dc949a8b2fb789f3cdc05f5d

# Download url is: https://update.code.visualstudio.com/commit:${commit_id}/server-linux-x64/stable
curl -sSL "https://update.code.visualstudio.com/commit:${commit_id}/server-linux-x64/stable" -o vscode-server-linux-x64.tar.gz

mkdir -p ~/.vscode-server/bin/${commit_id}
# assume that you upload vscode-server-linux-x64.tar.gz to /tmp dir
tar zxvf /tmp/vscode-server-linux-x64.tar.gz -C ~/.vscode-server/bin/${commit_id} --strip 1
touch ~/.vscode-server/bin/${commit_id}/0
```

Using fuzzwuzz python module to fuzzycompare files:
```
from fuzzywuzzy import fuzz
import sys
import codecs

with codecs.open("7750_sort_uniq.csv", 'r', encoding='utf-8',
                 errors='ignore') as a_file:
        with codecs.open("ISP_sort_uniq.csv", 'r', encoding='utf-8',
                 errors='ignore') as b_file:
                for a_line in a_file:
                        Str1 = a_line.strip().lower()
                        # print(Str1)
                        b_file.seek(0,0)
                        for b_line in b_file:
                                Str2 = b_line.strip().lower()
                                Ratio = fuzz.ratio(Str1,Str2)
                                if Ratio > 70:
                                        print("%d match :BETWEEN: %s :AND: %s" % (Ratio, Str1 , Str2))
                                        
```                                        

Python to convert PEM to JSON:
```
import json
import os
import ssl
import sys
from collections import OrderedDict
from pprint import pprint as pp

def main():
    debug = False
    if len(sys.argv) == 3:
      if sys.argv[2] == "-d":
        debug = True

    if debug:
      print("Python {:s} on {:s}\n".format(sys.version, sys.platform))
      print("cli arg1: {:s}\n".format(sys.argv[1]))

    cert_file_name = os.path.join(os.path.dirname(__file__), sys.argv[1])
    try:
        ordered_dict = OrderedDict()
        ordered_dict = ssl._ssl._test_decode_cert(cert_file_name)
        if debug: pp(ordered_dict)

    except Exception as e:
        print("Error decoding certificate {:s}: {:s}\n".format(cert_file_name,e))

    print(json.dumps(ordered_dict))

if __name__ == "__main__":
    main()

```

To the best of my knowledge, syslog-ng and rsyslog (the default) are the only ones available on RHEL. You could either probe the process space, see which process currently holds /var/log/syslog open or simply check which syslog daemon is installed (though, it's possible to have them both installed at the same time).


$ lsof /var/log/messages /var/log/syslog 2>&1 | grep syslog
$ rpm -q rsyslog syslog-ng
$ pgrep -u root syslog | xargs ps -p

Powershell Base64 Encoding:
```
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes("Y3Nwcm9vdDpjc3Byb290"))
```

'iptables -I INPUT -p tcp --dport 1022 -j ACCEPT'

command start-process PowerShell -verb runas


 zip -r ioneers.zip ioneers.net/ -x *node_modules/* -x *deploy/*
 
 find -maxdepth 1 ! -name node_modules ! -name . -exec rm -rv {} \;
 
 https://stackoverflow.com/questions/2065447/how-do-i-exclude-a-folder-when-performing-file-operations-i-e-cp-mv-rm-and-ch
 