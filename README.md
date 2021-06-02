# OpenBSD on demand mesh host

![Puffy rules](https://www.openbsd.org/art/puffy/ppuf800X725.gif)

The final goal is to add a full configured, secure by default, encrypted mesh node to an existing network. 

It will replicate all the services on the network, and it can be deleted without loosing any data.

Especially focused above security in every ISO/OSI pile level. 

Applications are multiples, from bypass the European ECHELON, an enormous sniffer from some ISP, or the great firewall in China, to create very secure not logged chat, to dynamic traditional services that will move from an host to another in a total transparent mode to the final user.

I'm an addicted of privacy and security and I'm very tired about the modern slavery network transmitted by weapons from the European elite. 

**Vatican and Aristocracy are totally guilty about the recent destroy of democracy.**

### Install procedure

First of all you've got to rent a VPS in one service provider, there are a lot on Internet a great resource to find the correct one is this website:

- [Low End Box - Cheap VPS, Dedicated Servers and Hosting Deals](https://lowendbox.com/)

Some that I use or I've used:

- [SSD VPS Servers, Cloud Servers and Cloud Hosting by Vultr - Vultr.com](https://www.vultr.com/)
- [AlphaVPS - Cheap and Reliable Hosting and Servers](https://alphavps.com/)
- [VPS Hosting in Europe and USA. Join VPS2DAY now!](https://www.vps2day.com/)
- [Liveinhost Web Services &#8211; The Best Web Hosting | Fast Professional Website Hosting Services](https://www.liveinhost.com/)
- [Scaleway Dedibox | The Reference for Dedicated Servers  | Scaleway](https://www.scaleway.com/en/dedibox/)

Try to understand that we've got to build a network of VPS interconnected site to site between everyone with IPsec and every host is plug and play, I mean that we can add or remove VPS just running the software in this repository. First of all it is important to understand that we can use this design in two different application, one will use registered domains the other will use free dns services. Goal for everyone is security trough simplicity, open source design and the correct use and implementation of robust compliance protocols and daemons. The system operative is [OpenBSD](https://www.openbsd.org/) but later we will use also [Alpine Linux](https://alpinelinux.org/). At that point the goal will be interoperability and the search of near perfect TCP/IP throughput. Another goal will be the use of ARM64 mobile devices also based up Alpine, my favorite one is:

-  [PinePhone](https://pine64.com/product-category/pinephone/?v=0446c16e2e66)

Another interesting end device based upon open hardware that use [LoRa](https://en.wikipedia.org/wiki/LoRa) and GSM is:

- [ESPboy](https://www.espboy.com/)

#### VPS without OpenBSD as system available

Many times we've got to resolve problems like the one where OpenBSD isn't listed as a default system operative in our remote KVM administration web console. This isn't our death.

First of all install a classic Linux, like Debian for example. Next ssh to the new machine with the credentials provided. Next download the latest stable `miniroot` image into the root and write it to the start of our virtual disk, in linux normally  it will be `vda`.

```sh
# wget https://cdn.openbsd.org/pub/OpenBSD/6.9/amd64/miniroot69.img
# dd if=miniroot69.img of=/dev/vda bs=4M
```

 After the successful write to the virtual disk we've got to reboot the machine but we will do it in a particular way using the `proc` filesystem:

```shell
# echo s > /proc/sysrq-trigger
# echo b > /proc/sysrq-trigger 
```

Next reopen the KVM web console and the installation process of OpenBSD will start. Interrupt it choosing for the (S)hell option and:

```shell
# dhclient vio0
# cd /tmp && ftp -o install.conf https://bit.ly/3mEYdAo
# install -af /tmp/install.conf
# reboot
```

After the reboot login in the new node and change the password and upgrade the system with `syspatch`.

#### First steps

First of all I want to underline that we use some values in the `DNS` master zone of the domain where we want to attach our new `VPS` host. *It's not exactly all automatic*.

``` shell
root@ganesha:/var/nsd/zones/master# cat telecomlobby.com.zone | grep ipsec && cat telecomlobby.com.zone | grep gre
ipsec20591		IN TXT "uk:ganesha;us:saraswati;jp:shiva;es:indra;fr:uma;bg:neo;"
gre7058			IN TXT "216"
gre18994		IN TXT "3"
root@ganesha:/var/nsd/zones/master#
```

We use the [TXT record](https://en.wikipedia.org/wiki/TXT_record) to add some more information to the process of automatically add the new host to our MESH network. Hostname are:

```shell
root@ganesha:/var/nsd/zones/master# echo ipsec${RANDOM} && echo gre${RANDOM} && echo gre${RANDOM}
ipsec6150
gre9262
gre1331
root@ganesha:/var/nsd/zones/master# 
```

```$RANDOM``` is a special variable in `ksh` used to generate random numbers between 0 and 32767.

The string specified by `TXT` value of `ipsec` is `;` separated values and contain [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) [country codes](https://en.wikipedia.org/wiki/Country_code) followed by `:` and the name of the host machine.

The string specified by `TXT` values of the two `gre` are integer, the first between  0 and 255 indicating last /30 network allocated by a `gre` point to point and the second is a counter indicating the number of MESH guerrilla OpenBSD hosts.

Remember to update those TXT to archive the connection process.

Next that we will have a running fresh and patched OpenBSD system let's start to configure our guerrilla MESH node. Install the git package:

```shell
neo# pkg_add git
neo$ mkdir -p Sources/Git && cd Sources/Git
neo$ git clone https://github.com/noplacenoaddress/OpenBSD.git
```

Next let's start to configure the system with our script `setup_node`, you've got to go ahead to every point pressing `1` or to type different variables:

- `hostname`, the name of the machine.
- `routerid`, the OSPFD router id and the IP of the `vether0` interface.
- `publichost`, the DNS of the public ip of the `vio0` interface.

```shell
root@neo:/home/taglio/Sources/Git/OpenBSD# sh setup_node                                                                                                                                                                                                  
changing installurl
Go ahead type 1 
```

#### Remote upgrade

![](https://redama.es/Imagenes/varuna_shell.png)

If the VPS provider got the option to install OpenBSD, a custom ISO or hasn't the solution is always the same, use `sysupgrade`.

The upgrade our git repository and launch the `upgrade.sh` script. Remember to wait a couple of days after the [release announce](https://www.openbsd.org/69.html) is published by [Theo de Raddt](https://www.theos.com/deraadt/).

#### Registered domains application

Start with two VPS, one master in DNS service and the other slave. All the others services will be replicated. Some providers doesn't permit the installation of OpenBSD as a default option so install Linux and then rewrite the disc with `dd` as explained:

#### Possible applications

Let's start discussing how we can boost our presence in Internet using that guerrilla MESH system that guaranty our privacy and security on the web. My first project is regarding a website replicated over different countries in all the languages over the world. Something very important to obtain maximum results about clients, about web goods sell, about be recognized over it and about whatever we want to obtain searching for visibility.

I've got to cases, one is about the correct information about the modern slavery network transmitted by electromagnetic weapons to the marginal and worker class of many countries, also Europeans. The other is to have got a great site about my professional work, Redama, a wireless ISP but also an Internet website that sold security focused end devices and gateways.   

Nice Regards,

Riccardo `<taglio>` Giuntoli.