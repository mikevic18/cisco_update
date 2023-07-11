# Updating Cisco Access Points (APs) using TFTP

## Introduction
This guide will walk you through the process of updating Cisco Access Points (APs) using Trivial File Transfer Protocol (TFTP). TFTP is a simple file transfer protocol commonly used for network device configurations and firmware upgrades. By following these steps, you will be able to update the firmware on your Cisco APs and ensure they are running the latest software.

## Prerequisites
Before you begin, make sure you have the following prerequisites in place:

1. TFTP server: 
    
    Install and configure a TFTP server on your local machine or network. You can use popular TFTP server software such as Tftpd32 (for Windows) or tftpd-hpa (for Linux).

2. Cisco AP firmware: 
    
    Obtain the latest firmware file for your Cisco AP model from the Cisco website or your network equipment vendor. Ensure that you have the correct firmware file for your AP model and that it is compatible with your network infrastructure.

3. Network connectivity: 

    Ensure that the APs you want to update and the machine running the TFTP server are connected to the same network.

## Step-by-Step Guide(Newer APs)

1. Connect to the TFTP server: Start by connecting your machine to the TFTP server. Ensure that the TFTP server software is running and accessible.

2. Prepare the firmware file: Place the firmware file you obtained in the TFTP server's directory. The specific directory location may vary depending on the TFTP server software you are using. Refer to the documentation of your TFTP server software for the exact location.

3. Configure the Cisco AP: 

    Connect to the Cisco AP you want to update using a console cable and a terminal emulation program such as PuTTY or TeraTerm. Configure the AP's IP address and default gateway using the following commands:

    ```ap# configure terminal
    ap(config)# interface BVI1
    ap(config-if)# ip address <AP_IP_ADDRESS> <SUBNET_MASK>
    ap(config-if)# ip default-gateway <DEFAULT_GATEWAY_IP>
    ap(config-if)# end
    ``````

Replace `<AP_IP_ADDRESS>` with the desired IP address for the AP, and `<SUBNET_MASK>` and `<DEFAULT_GATEWAY_IP>` with the appropriate values for your network.
If you are having trouble finding your netmask you can calculate it using this website: https://www.subnetmask.info/

4. Verify connectivity: Verify that the AP can communicate with the TFTP server by pinging the server's IP address.

        ap# ping <TFTP_SERVER_IP_ADDRESS>

Replace `<TFTP_SERVER_IP_ADDRESS>` with the IP address of the TFTP server.

5. Enter privileged mode: 
    
    Enter privileged EXEC mode on the AP using the following command:
ap# enable


6. Update the AP firmware: Use the `archive` command to initiate the firmware update process.
    ```
    ap# archive download-sw /overwrite tftp://<TFTP_SERVER_IP_ADDRESS>/<FIRMWARE_FILE_NAME>
    ```

Replace `<TFTP_SERVER_IP_ADDRESS>` with the IP address of the TFTP server and `<FIRMWARE_FILE_NAME>` with the name of the firmware file you placed in the TFTP server directory.

7. Follow the prompts
    
    The AP will prompt you to confirm the firmware update. Press `Enter` to proceed.

8. Monitor the update progress
    
     The AP will download the firmware file from the TFTP server and update its software. Monitor the progress of the update process until it completes successfully.

9. Verify the update: 
    
    Once the update is complete, verify that the AP is running the new firmware version using the following command:
    ```
    ap# show version
    ```
Review the output to ensure that the firmware version has been updated as expected.

10. Repeat for other APs (if applicable): If you have multiple APs to update, repeat steps 3 to 9 for each AP individually.

## Step-by-Step Guide(Older APs)
The process for updating older Cisco APs is similar to the process for newer APs, with a few minor differences. Follow the steps below to update older Cisco APs using TFTP.

1. Configure the TFTP server: 

    Start by configuring the TFTP server. Ensure that the TFTP server software is running and accessible. Set the TFTP server directory to the location where you have the firmware file stored.

2. Connect to the Cisco AP: 

    Connect to the Cisco AP you want to update using a console cable and a terminal emulation program such as PuTTY or TeraTerm.

3. Configure the AP's IP address, netmask, and default router:

     Use the following commands to set the IP address, netmask, and default router on the AP:
        
        ap# set IP_ADDR <AP_IP_ADDRESS>
        ap# set NETMASK <SUBNET_MASK>
        ap# set DEFAULT_ROUTER <DEFAULT_GATEWAY_IP>
    Replace `<AP_IP_ADDRESS>` with the desired IP address for the AP, `<SUBNET_MASK>` with the subnet mask, and `<DEFAULT_GATEWAY_IP>` with the IP address of the default gateway.
    
    If you are having trouble finding your netmask you can calculate it using this website: https://www.subnetmask.info/

4. Initialize the Ethernet, Flash interfaces and TFTP:
    ```
    ap# ether_init
    ap# flash_init
    ap# tftp_init
    ```
5. Verify connectivity: Verify that the AP can communicate with the TFTP server by pinging the server's IP address. 
    ```
    ap# ping <TFTP_SERVER_IP_ADDRESS>
    ```

6. Extract the firmware file from the TFTP server: 
    
    Use the following command to extract the firmware file from the TFTP server and copy it to the Flash memory on the AP:
    ```
    ap# tar -xtract tftp://<TFTP_SERVER_IP_ADDRESS>/<FIRMWARE_FILE_NAME> flash:
    ```

Replace `<TFTP_SERVER_IP_ADDRESS>` with the IP address of the TFTP server and `<FIRMWARE_FILE_NAME>` with the name of the firmware file.

7. Set the boot file path: 
    
    Use the following command to set the boot file path on the AP:

    ```
    ap# set BOOT flash:/NEWPATH/update_fileName
    ```

Replace `NEWPATH` with the appropriate path on the Flash memory and `update_fileName` with the name of the firmware file(usually containing mx not xx).

8. Boot into the new firmware: 
    ```
    ap# boot
    ```



10. Verify the update: 

    Once the update is complete, verify that the AP is running the new firmware version using the appropriate command for your AP model. 
    ```
    ap# show version
    ```

11. Repeat for other APs (if applicable): 
    
    If you have multiple APs to update, repeat steps 2 to 10 for each AP individually.

## Troubleshooting

If you encounter any issues while updating Cisco APs using TFTP, consider the following troubleshooting steps:

1. Timed out transfers: If you are experiencing timed out transfers during the update process, it may indicate that the AP does not have the correct subnet, IP address, or default router configured. Ensure that you have correctly set the IP address, netmask, and default router on the AP using the appropriate commands mentioned in the previous section.

2. TFTP server accessibility: Verify that the TFTP server is running and accessible from the AP. Ensure that the TFTP server software is properly installed and that the firmware file is stored in the correct directory specified by the TFTP server. Also, ensure that there are no firewall or network restrictions preventing communication between the AP and the TFTP server.

3. Check firmware file compatibility: Ensure that you have obtained the correct firmware file for your specific older Cisco AP model. Verify the firmware file's compatibility with your AP model and the network infrastructure. Using an incompatible firmware file can result in unsuccessful updates or unexpected behavior.

4. Flash memory availability: If the AP's flash memory is full or has insufficient space to accommodate the new firmware file, the update process may fail. Check the available space on the AP's flash memory using the appropriate command for your AP model. If necessary, free up space by removing unnecessary files or consider upgrading the AP's flash memory.

5. Verify network connectivity: Ensure that the AP and the machine running the TFTP server are connected to the same network. Verify the network connectivity by pinging the TFTP server from the AP using the appropriate command. If the ping fails, troubleshoot the network connectivity issues between the AP and the TFTP server.

6. Console connection stability: Ensure that the console connection between the AP and the terminal emulation program is stable. A faulty or intermittent console connection can lead to communication issues during the update process. Check the console cable connections and consider using a different cable if necessary.

If you are still facing issues(expecially timed out transfers) after troubleshooting, before going any further make sure to reboot the device into recovery again and assign it an available IP address whilist also making sure that the netmask and router IP address are correct. If this does not change anything consult the Cisco documentation or seek assistance from Cisco support or your network equipment vendor for further assistance.
