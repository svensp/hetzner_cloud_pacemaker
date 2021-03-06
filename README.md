# DEPRECATED!
This package is in the process of being rewritten in python: https://github.com/svensp/hcloud_ocf
The reason for this is that it already requires python to parse the api response anyway, might as well write the whole thing in it to take advantage of OOP and better error handling via exceptions.

# hetzner\_cloud\_pacemaker
Hetzner Cloud floating ip resource and api stonith device  
No installation routine is provided as I use it by copying the files via
ansible.  
Pathes mentioned here are taken from debian 9 and might differ for other
distributions or versions.

## Requirements
Both scripts require curl and python3  
Both require python3 despite being written in shell because a small python
snippet is used to parse the json returned from the cloud api.

## Recommendations
Api requests will, at times, take too long to answer and thus get timed out by
pacemaker. I have not yet found a concrete reason why this happens but I
attribute it to the api being offline or the network connection to it not
working.
To circumvent this I recommend setting the `meta` attribute `fault-timeout`.  It
causes pacemaker to forget about past errors after a number of seconds. 

## FloatingIP
Is an ocf resource agent written in shell for pacemaker. It reroutes the
floating ip to the host which it is run on.

!Note! The hostname and its name in the cloud api MUST be the same for this
to work.

It is ment to reside in /usr/lib/ocf/resource.d/hetzner/ and used as
ocf:hetzner:FloatingIP  
It takes the following parameters:
- ip: Ip-Address of the floating ip to be managed
- api\_token: An api token with which the cloud api can be accessed to manage the
  floating ip

## hetzner\_cloud
is a stonith agent written in shell. It manages the host given as hostname
parameter through the cloud api, using the poweron, poweroff and reset actions.

It is ment to reside in /usr/lib/stonith/plugins/external/ and used as
stonith:external/hetzner\_cloud
It takes the following parameters:
- api\_token: An api token with which the host is managed
- hostname: The name of the host which this stonith agent should manage
