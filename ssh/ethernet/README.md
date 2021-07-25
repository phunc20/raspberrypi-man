# Idea
The idea is to ssh connect into a Raspberry Pi from a laptop **thru an Ethernet cable**.

The ssh command part is not different from ssh into RPi from a laptop thru a wifi network.
The reason why we want to do this is that, if our RPi is headless, then we could not know
the ip address assigned to RPi by our router. Be it at home or in a cafe, that would be
quite inconvenient if we want to use our RPi along with our laptop.


## `/etc/dhcpcd.conf`
To Fix the ip address of RPi, go edit the file `/etc/dhcpcd.conf` in your RPi machine and make sure you have the
following lines:
```
interface eth0
static ip_address=10.10.10.10/24
```

Note that you can set the static ip of your RPi anything you want, as long as it lies in the following range:
```
10.0.0.0 ~ 10.255.255.255 (Class A)
172.16.0.0 ~ 172.31.255.255 (Class B)
192.168.0.0 ~ 192.168.255.255 (Class C)
```

**cf.** [explanation in Chinese on Class A/B/C static ip](https://www.opencli.com/internet/why-router-use-192-168-x-x?fbclid=IwAR3KmD5WNrMJtFlB_0zU5DP7FYx_27c_XNjPRj0KDu8NUfpGp8Qvj5vOthI)

## Now That RPi's IP Fixed, How to Make A Valid SSH Connection?
Well, you need to set the interface connecting to the RPi to the same **domain**.
> Identical first three `uint8` $\implies$ **same domain**, e.g. `172.16.7.8` and `172.16.7.177` lie in
> the same domain, while `192.168.1.2` and `192.168.200.2` do not lie in the same domain.

If you often make such an Ethernet connection to your RPi this way, in order to automate this task,
you may write the following into to a shell script.

```
# find the name of the ethernet interface of your laptop
ip -br a  # or an equiv. ifconfig command
# For example, you've found out that the interface is named enxf01e341479dc.
# Then set the interface enxf01e341479dc to some ip under the same domain, say, 10.10.10.100
sudo ip address add 10.10.10.100/24 dev enxf01e341479dc
# If you set it wrong, you can use the following command to delete it
#sudo ip address del 10.10.10.100/24 dev enxf01e341479dc
# Finally, ssh into RPi: The default login and password of RPi are pi and raspberry, resp.
ssh pi@10.10.10.10
```
