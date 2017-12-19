# PocketPilot
PocketPilot boards are smaller versions of the BBBmini PCB (https://github.com/mirkix/BBBMINI), designed for use with PocketBeagle (https://beagleboard.org/pocket, a small version of BeagleBone Black). It provides a very small & light-weight open-source ArduPilot based autopilot / flight controller for quadcopter drones & robots.

There will be several different versions of the PocketPilot board coming out over time:

* **PocketPilot v1** (DIY design, early 2018): Only uses through-hole components & connectors, with Chinese sensor modules plugged in. Since it doesn't need any SMD soldering, you are expected to build it yourself using traditional solder by following our instructions. Basically it's a BBBmini for the PocketBeagle computer instead of for BeagleBone Black.
* **PocketPilot v2** (mid 2018): Similar to PocketPilot v1 but uses JST-GH connectors, and for people that can't do any soldering themselves. It can be ordered from Shervin in Australia, or soldered yourself if you are willing to solder a few SMD connectors.
* **PocketPilot v3** (late 2018): A small & lightweight single board containing SMD parts and JST-GH connectors and no Chinese sensor modules, manufactured by a PCBA service in China and ordered from China. Includes support for hundreds of <a href="https://www.mikroe.com/click">MikroEletronika CLICK accessory boards</a> stacked on top.

PocketPilot v1 (based on BBBmini https://github.com/mirkix/BBBMINI-PCB) can be soldered by hand by most people since it only uses large "through-hole" components and common "Dupont" servo wiring connectors. However, it's quite big & heavy compared to the small PocketBeagle, and the connectors can potentially have a loose connection when on a moving robot or quadcopter, and also not everyone wants to solder their own electronics. So PocketPilot v3 will be using tiny SMD components, reliable SMD connectors, and available as a pre-soldered kit. All PocketPilot boards are open-source hardware, just like BBBMini, but most people will only be capable of soldering v1 and maybe v2.

![Connector Stack diagram](https://raw.githubusercontent.com/PocketPilot/PocketPilot/master/doc/Initial%20Prototype/Connector%20Stack.png)


## Initial Prototype

[![Video of initial prototype](https://img.youtube.com/vi/BBnUvO6x0oY/0.jpg)](https://youtu.be/BBnUvO6x0oY)

![Wiring diagram of prototype](https://raw.githubusercontent.com/PocketPilot/PocketPilot/master/doc/Initial%20Prototype/Pocket%20Wiring.jpg)

![Photo of initial prototype](https://raw.githubusercontent.com/PocketPilot/PocketPilot/master/doc/Initial%20Prototype/Pocket%20Circuit.jpg)

Original description of the Initial Design (ie: selection of pins & ports needed) is at 
https://github.com/shervinemami/BBBmicro-PCB/wiki/Initial-Design


## Support Chat

[![Join the chat at https://gitter.im/mirkix/BBBMINI](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/mirkix/BBBMINI?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)


## About Us
BBBmini is a project by <a xmlns:cc="http://creativecommons.org/ns#" href="https://github.com/mirkix" property="cc:attributionName" rel="cc:attributionURL">Mirko Denecke</a>.
PocketPilot is a joint project by <a href="https://github.com/mirkix">Mirko Denecke</a>, <a href="https://github.com/patrickpoirier51">Patrick Poirier</a> and <a href="https://github.com/shervinemami">Shervin Emami</a>.


## License

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br /><span xmlns:dct="http://purl.org/dc/terms/" property="dct:title">BBBmini and PocketPilot</span> are licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.

UNLESS OTHERWISE MUTUALLY AGREED TO BY THE PARTIES IN WRITING, LICENSOR OFFERS THE WORK AS-IS AND MAKES NO REPRESENTATIONS OR WARRANTIES OF ANY KIND CONCERNING THE WORK, EXPRESS, IMPLIED, STATUTORY OR OTHERWISE, INCLUDING, WITHOUT LIMITATION, WARRANTIES OF TITLE, MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE, NONINFRINGEMENT, OR THE ABSENCE OF LATENT OR OTHER DEFECTS, ACCURACY, OR THE PRESENCE OF ABSENCE OF ERRORS, WHETHER OR NOT DISCOVERABLE. SOME JURISDICTIONS DO NOT ALLOW THE EXCLUSION OF IMPLIED WARRANTIES, SO SUCH EXCLUSION MAY NOT APPLY TO YOU. EXCEPT TO THE EXTENT REQUIRED BY APPLICABLE LAW, IN NO EVENT WILL LICENSOR BE LIABLE TO YOU ON ANY LEGAL THEORY FOR ANY SPECIAL, INCIDENTAL, CONSEQUENTIAL, PUNITIVE OR EXEMPLARY DAMAGES ARISING OUT OF THIS LICENSE OR THE USE OF THE WORK, EVEN IF LICENSOR HAS BEEN ADVISED OF THE POSSIBILITY OF SUCH DAMAGES.


## Changelog

Rev 0.0
* Discussion about initial design, at "https://github.com/shervinemami/BBBmicro-PCB/wiki/Initial-Design"
Rev 0.1
* Initial Prototype by Patrick Poirier, flown on a quadcopter "https://www.youtube.com/watch?v=BBnUvO6x0oY".
