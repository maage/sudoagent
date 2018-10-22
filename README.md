# Sudoagent

# Warning
Idea is tested and it works.

But actual scripts in the repo are not tested as is at the moment.

As scripts edit bash login script, sudo, sshd and pam config it is
possible you are locked out of your machine if there is a error. As
always test these when you have previously setup root sessions to
restore configs. Some familiarity with the technologies are needed to
undestand when you are testing this.

Some of the usage instructions and all the helps are missing.

# Why
I really detest using sudo and writing password remotely. It is really
easy to snoop and later reuse. I also detest having sudo to run all
commands without any auth at all (NOPASSWD). There is
pam\_ssh\_agent\_auth, but it uses ssh-agent to authorize sudo and it
really is bad idea to send ssh agent socket to remote host if it is not
trusted as your local workstation.

Idea is to add another agent to work as auth medhod for sudo calls only.
And not use normal ssh-agent as all to do remote auth.

You create keys with `sudoagent-keygen <name>` for some group of hosts.
And then add `~/.sudoagent/id_<name>*.pub` to those hosts at
`~/.sudoagent/authorized_keys`.

Now if someone tries to use forwarded sudoagent socket to something,
attacker can only annoy you by requiring extra ok from agent to run
sudo. As these keys are not used any other purpose, there is no
drawback.

It generally is not a good idea to forward ssh agent
(-A / ForwardAgent yes) because attacker can reuse agent to do further
connections.

One way to disabuse is to collect authorized\_keys from one system and
then use them at another system and fool you to accept sudo usage for
wrong system when you have active sudoagent enabled ssh agent
connections for both. This is possible to mitigate by using ssh-ident
like setup.

Another way to disabuse is when sudoers setting timestamp\_timeout is
not 0 as subsequent sudo runs rely on saved timeout file and attacker
can do stuff as you. And extend timeout by running `sudo -v`. But this
does not change at all compared to normal sudo usage. To mitigate
this, set timestamp\_timeout to 0 or use `sudo -k` or `sudo -K` right
after running sudo.

etc contains either fragments or edits
bin contains scripts to manage sudoedit

You need to allow port-forwarding at your real `~
/.ssh/authorized_keys` if restricted.

AllowStreamLocalForwarding and not DisableForwarding

You should set ForwardAgent to no for hosts used here.

## sudoagent-start
Start agent if not yet started.
To use agent you should eval output.
Example:

```sh
eval "$(sudoagent-start)"
```

Other scripts should also start sudoagent if it is needed so it is not
generally required. Currently there is only one instance of sudoagent.

## sudoagent-enable
Add all identities to agent.

## sudoagent-keygen
Generate new identity.

## sudoagent-client-setup
Setup client machine, mainly add login/logout triggers and setup
directories.

## ssh-sudoagent
ssh wrapped with sudoagent settings. Setup sudoagent socket forwarding
and disable auth forwarding.

# License
Copyright Cybercom Group AB

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.

# Author
Markus Linnala <Markus.Linnala@cybercom.com> 2018
