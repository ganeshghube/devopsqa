#############################################################################################

Challenge #1
============
A 3 tier environment is a common setup. Use a tool of your choosing/familiarity create these resources. Please remember we will not be judged on the outcome but more focusing on the approach, style and reproducibility.
Answer
============
Tier 3 Architecture has Presentation tier, Application tier and Data tier So following tools are required.
1. Presentation and Application tier would need EC2 ALB, Targetgroup, Apache Tomcat and java jdk installed on EC2 to serve UI and logic both. Expected network has been setup on cloud and respective security group and linux operating system.
2. Data Tier would required RDS PostgreSQL

#############################################################################################

Challenge #2
============
Summary
We need to write code that will query the meta data of an instance within aws and provide a json formatted output. The choice of language and implementation is up to you.

 
Bonus Points
The code allows for a particular data key to be retrieved individually

Answer : Execute following code :  python output.py | jq -r
===============================================================
#!/usr/bin/env python

import requests
import json


# Converts AWS EC2 instance metadata to a dictionary
def load():
    metaurl = 'http://169.254.169.254/latest'
    # those 3 top subdirectories are not exposed with a final '/'
    metadict = {'dynamic': {}, 'meta-data': {}, 'user-data': {}}

    for subsect in metadict.keys():
        datacrawl('{0}/{1}/'.format(metaurl, subsect), metadict[subsect])

    return metadict


def datacrawl(url, d):
    r = requests.get(url)
    if r.status_code == 404:
        return

    for l in r.text.split('\n'):
        if not l: # "instance-identity/\n" case
            continue
        newurl = '{0}{1}'.format(url, l)
        # a key is detected with a final '/'
        if l.endswith('/'):
            newkey = l.split('/')[-2]
            d[newkey] = {}
            datacrawl(newurl, d[newkey])

        else:
            r = requests.get(newurl)
            if r.status_code != 404:
                try:
                    d[l] = json.loads(r.text)
                except ValueError:
                    d[l] = r.text
            else:
                d[l] = None



if __name__ == '__main__':
    print(json.dumps(load()))

	
#############################################################################################
Challenge #3
============
We have a nested object, we would like a function that you pass in the object and a key and get back the value. How this is implemented is up to you.

 
Example Inputs
object = {“a”:{“b”:{“c”:”d”}}}
key = a/b/c
 
object = {“x”:{“y”:{“z”:”a”}}}
key = x/y/z
value = a
 
Hints
We would like to see some tests. A quick read to help you along the way

We would expect it in any other language apart from elixir.


Answer
========


a = {'a':{'b':{'c':'d'}}}

# Iterate over key / value pairs of parent dictionary
for key, value in a.items():
    print(key)
    # Again iterate over the nested dictionary
    for keya, score, in value.items():
        print(keya)
        for keyb, h in score.items():
            print (keyb, h[0])


Output  : 

a
b
c d
