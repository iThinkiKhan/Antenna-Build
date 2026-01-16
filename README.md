_Update 2: I ended up moving the to the attic with my OrangePi 02W (get one, they rock) as a local node for the RTL SDR. This has the dual advantage of not having to run cable, and keeping the signal crisp and clear (no cable degradation). It has a case that doubles as an aluminum heatsink and external 2.4Ghz antenna, which makes it ideal for this project. Im currently getting excellent wifi reception from the attic (-44 dbm), plenty sufficient for streaming the live data. The OrangePi is also setup as my home network sidecar, to restart things if the main lab ever loses power. My attic is not lined with foil insulation, so signal up there is excellent. I have to be careful not to clip out the RTL SDR. Early testing and some AI math shows my gain at about 700x by moving it up. Its also completely hidden (WAF) and all the data is processed locally or streamed over the air. Sweet!_

_Update 1: Initial testing shows the antenna to be suprisingly good. From my basement I am able to pick up NOAA clear from about 30 miles away. No problem picking up ship transponders from Lake Michigan, despite them being at a lower elevation, 15-20 miles away and the antenna being in a concrete half-basement. The suprising effectiveness of the antenna has led me to consider moving it to the attic rather than outside._


This is a home made Dual-Band Fan Dipole style antenna I made out of common scrap materials. You could make this out of about anything - wire hangers and solder would do a pretty good job too. You do need an insulated coax cable for this design - the ones comcast gives out with new boxes work great. This can be totally free if you have the scrap, and is 10-20 dollars if you don't. It is surprisingly effective, especially using copper elements. The screw block terminals were chosen for stability, and largely determined the size of the pvc pipe. I had to shave the screw blocks down a bit to get them to fit snugly into the 1.25 pvc.

The antenna is optimized for two specific frequency ranges:
1.  **VHF High Band (~152 MHz):** Targeting Rail (160-161 MHz), Public Safety (150-159 MHz), and Air Traffic Control. This also gives me good resonance on 433 MHz, which is active in my neighborhood. 
2.  **UHF ISM Band (~900 MHz):** Targeting Smart Meters, LoRa, and Sub-GHz IoT devices. Eventually can be a hub for farther ranging smart devices.

The design prioritizes stealth and durability, using standard PVC housing and readily available hardware store materials.

## Bill of Materials

* **Antenna Elements:** 12 AWG solid copper wire (stripped from Romex, or bought by the foot. 5ft required).
* **Transmission Line:** RG6 Coax cable (Comcast/standard CATV grade).
* **Connector:** 2-pole Screw Terminal Block.
* **Housing:** 1.25-inch PVC pipe with end caps.
* **Grounding:** Standard F-Connector Ground Block (bonded to main house ground via cable box prior to passthrough to house).
* **Receiver:** RTL-SDR V4 Blog Dongle.



## Hardware Assembly

### 1. The Terminal Block Core
The center of the antenna uses a standard terminal block to join the elements to the coaxial cable. The 12g wire can be a bear, as you can tell from the photos. Doesn't have to be perfect, as we are just receiving. This gives a strong connection to the grounding shield of the cable.

* **Configuration:** The "Fan" design connects two wires to *each* terminal screw. One long wire (VHF) and one short wire (UHF) share the same connection point. Hence the Dual Band.
* **Wire Shaping:** The wires are spread into an "X" pattern to minimize interference between the VHF and UHF elements, as much as the pipe will allow.

### 2. Coax Connection (Aluminum Braid Fix)
Standard RG6 cable often uses an aluminum foil/braid shield which cannot be soldered and crumbles when twisted. A "Compression Clamp" method was used to ensure a solid ground connection. You can use extra 12g or even just bell wire. i ended up going with bell wire, it is much easier to work with. The idea is the same, clamp the heck out of the aluminum shield with you copper, and hook that to the downward facing wires via screw block.

* **Center Conductor:** Stripped and inserted directly into the **Right** (upwards) terminal.
* **Shield/Ground:**
    * The outer jacket was stripped without peeling back the foil/braid.
    * A scrap piece of copper wire (12 AWG or Bell wire) was formed into a "U" shape.
    * This copper "U" was clamped *over* the foil shield (supported by the internal foam dielectric) and twisted tight with pliers.
    * The twisted copper legs were inserted into the **Left** terminal.
* **Safety Check:** Watch for shorts.

### 3. Tuning 
The elements were trimmed to specific lengths to resonate at the target frequencies. Measurements are taken from the edge of the terminal block.

| Band | Target Frequency | Element Length |
| :--- | :--- | :--- |
| **VHF** | 152 MHz | **18.5 inches** |
| **UHF** | 915 MHz | **3.0 inches** |

*Note: The VHF elements were cut slightly shorter than the standard 19.25" (146 MHz) to favor the 160 MHz Rail band.*

### 4. Housing and Mounting
* **Enclosure:** The assembly fits inside a 1.25" PVC pipe.
* **Stealth:** The pipe is painted with a matte camouflage pattern to blend into foliage. Youll need to use a fine sandpaper to score your pvc and remove the glossy coating. **Be sure not to use metallic paint!**
* **Stabilization:** A sponge is wrapped around the terminal block to friction-fit the assembly inside the pipe.
* **Mounting:** Designed for vertical mounting. From a tree, fence post, roof, whatever. Its currently working very well in my half basement.

---

## Software Configuration

I chose to hook the SDR dongle into my Proxmox machine in my homelab. Proxmox is good at this - no issues. You MUST use the v4 drivers the RTL SDR if you have the v4 dongle. I passed the dongle through to Kali Linux and installed rtl-sdr, which broadcasts the data on my network using rtl_tcp. I then use SDR sharp to pick up the data upstairs for analysis, or listening. 

The Python script is a little bonus. Very efficient at converting .sub files (from Flipper0) to .complex files for rtl_433 analysis!
