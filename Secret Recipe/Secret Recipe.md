# Secret Recipe

You will be provided a folder with Eric Zimmerman tools. In order to answer the questions, the primary tool we will be utilizing is Registry Explorer.

## Task 1
- `Connect with the lab.`

    Done - No answer needed.

- `How many files are available in the "Artifacts" folder on the desktop?`

    6 - The desktop should contain:
    - SYSTEM
    - SECURITY
    - SOFTWARE
    - SAM
    - NTUSER.DAT
    - UsrClass.dat

## Task 2 

1. Start Registry Explorer from the "Tools" folder. 
2. Click: File - "Load Hive" and navigate to the desktop Artifacts folder and load all of the hives provided.

- `What is the Computer Name of the Machine found in the registry?`

    The computer name is found in the Registry key: SYSTEM\CurrentControlSet\Control\ComputerName
\ComputerName
    
    You can either navigate there in Registry Explorer, or my personal choice is Registry Explorers "Bookmarks" which parse useful information for you. 

    Click "Bookmarks", drop down the SYSTEM hive and find "Computer Name"

    The Data is the ComputerName value is "James"
    
    ![ComputerName](https://github.com/missinglenk13/THM_Walk_Throughs/blob/main/Secret%20Recipe/Images/ComputerName.png)

- `When was the Administrator account created on this machine? (Format: yyyy-mm-dd hh:mm:ss)`

    User account information is found under the SAM hive.

    In the "Registry Hives" tab, navigate to SAM\Domains\Account\Users\Names\Administrator and view the value for the "Last Write timestamp". 2021-03-17 14:58:48

    ![Admin](https://github.com/missinglenk13/THM_Walk_Throughs/blob/main/Secret%20Recipe/Images/Admin_Account.png)

- `What is the RID associated with the Administrator account?`

    You can find the RID for the Admin Account in the same location as the previous answer. The "Value" for the Administrator Account is 0x1F4, which is 500.

    ![Admin_RID](https://github.com/missinglenk13/THM_Walk_Throughs/blob/main/Secret%20Recipe/Images/Admin_RID.png)

- `How many User accounts were observed on this machine?` 

    Within the same registry key - you can count the number of keys under the "Names" value. There are 7 accounts.

    ![User_Accounts](https://github.com/missinglenk13/THM_Walk_Throughs/blob/main/Secret%20Recipe/Images/Number_Users.png)

- `There seems to be a suspicious account created as a backdoor with RID 1013. What is the Account Name?`

    Regardless of the fact that there is a suspicious user accoutn name "bdoor" we can click through the user accounts to find RID 1013. 

    ![RID_1013](https://github.com/missinglenk13/THM_Walk_Throughs/blob/main/Secret%20Recipe/Images/RID_1013.png)


- `What is the VPN connection this host connected to?`

    WE can find a list of networks that the machine is connected to in the SOFTWARE\Microsot\Windows NT\CurrentVersion\NetworkList

    I like to use the easy button of going to the "Available Bookmarks" tab and dropping the "SOFTWARE" hive down and clicking on "NetworkList". In there you can see ProtonVPN connection.

    ![ProtonVPN](https://github.com/missinglenk13/THM_Walk_Throughs/blob/main/Secret%20Recipe/Images/Proton_VPN.png)

- `When was the first VPN connection observed? (Format: YYYY-MM-DD HH:MM:SS)`

    Within the same key you can find the FIrst Connection time of 2022-10-12 19:52:36.

    ![ProtonVPN_First](https://github.com/missinglenk13/THM_Walk_Throughs/blob/main/Secret%20Recipe/Images/VPN_First_connect.png)

- `There were three shared folders observed on his machine. What is the path of the third share?`
    
    Again, I like to use the easy "bookmark" button. You can find the shared folder information in SYSTEM\ControlSet001\Services\LanmanServer\Shares or find the "Shares" bookmark under the SYSTEM hive in the Bookmarks section. Find the third shared folder. 

    ![Shares](https://github.com/missinglenk13/THM_Walk_Throughs/blob/main/Secret%20Recipe/Images/Third_share.png)

- `What is the Last DHCP IP assigned to this host?`

    You can find the DHCPIP addressing information in SYSTEM\ControlSet001\Services\Tcpip\Parameters\Interfaces or just click "Interfaces" in the System hive bookmark.

    ![DHCP](https://github.com/missinglenk13/THM_Walk_Throughs/blob/main/Secret%20Recipe/Images/DHCPIP.png)

- `The suspect seems to have accessed a file containing the secret coffee recipe. What is the name of the file?`

    I found this by analyzing the key "RecentDocs". This will let us know what files that the user had accessed: SOFTWARE\Microsoft\WIndows\CurrentVersion\Explorer\RecentDocs

    I found secret-recipe.pdf indicating that file is likely the "secret coffee recipe" we are looking for. 

    ![Secret Recipe](https://github.com/missinglenk13/THM_Walk_Throughs/blob/main/Secret%20Recipe/Images/Secretrecipe.png)

- `The suspect ran multiple commands in the run windows. What command was run to enumerate the network interfaces?`

    The commands run in the run start menu can be found in the RunMRU key located in SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\RunMRU

    The executable that appears to enumerate network interfaces is the second entry of "pnputil /enum-interfaces".

    ![RunMRU](https://github.com/missinglenk13/THM_Walk_Throughs/blob/main/Secret%20Recipe/Images/RunMRU.png)

- `In the file explorer, the user searched for a network utility to transfer files. What is the name of that tool?`

    The key named "WordWheelQuery" is a historical repository of search terms typed into the search bar in windows file explorer. The user at one point had searched for the tool "netcat"

    ![netcat](https://github.com/missinglenk13/THM_Walk_Throughs/blob/main/Secret%20Recipe/Images/netcat.png)

- `What is the recent text file opened by the suspect?`

    Back to the "RecentDocs" key to find out recent files opened by the user. The most recent files would be indicated by the "Mru Position", the lower the number, the more recent the interaction, with 0 being the most recent interacted document. The most recent text file is "secret-code.txt".

    ![Secret Code](https://github.com/missinglenk13/THM_Walk_Throughs/blob/main/Secret%20Recipe/Images/SecretCode.png)

- `How many times was Powershell executed on this host?`

   The following key contains a few GUID subkeys. We are looking for the GUID that starts with CEBFF5CD which is for Executable File Execution. "\Software\Microsoft\Windows\CurrentVersion\Explorer\UserAssist\{GUID}\Count"

   Scroll down to find powershell and take note of the count of 3.

   ![PowerShellCount](https://github.com/missinglenk13/THM_Walk_Throughs/blob/main/Secret%20Recipe/Images/PowershellCount.png)

- `The suspect also executed a network monitoring tool. What is the name of the tool?`

    In the same key we can scroll to find different executables that were run. If we were looking for a network monitoring tool, we would notice Wireshark being ran from the Downloads folder. 

    ![wireshark](https://github.com/missinglenk13/THM_Walk_Throughs/blob/main/Secret%20Recipe/Images/wireshark.png)

- `Registry Hives also notes the amount of time a process is in focus. Examine the Hives. For how many seconds was ProtonVPN executed?`

    Within the same key as well we can see a column that states the Focus times. If we scroll to ProtonVPN we can see that the application was in focus for 5 minutes and 43 seconds... OR 343 seconds.

    ![FocusTime](https://github.com/missinglenk13/THM_Walk_Throughs/blob/main/Secret%20Recipe/Images/FocusTime.png)

- `Everything.exe is a utility used to search for files in a Windows machine. What is the full path from which everything.exe was executed?`

    Finallly, again within the UserAssit key we can find where everything.exe was run from. For time sake, instead of scrolling through, Registry Explorer has a search function and I search for "everything" and it found the programs full file path of C:\Users\Administrator\Everything\Everything.exe

    ![everything](https://github.com/missinglenk13/THM_Walk_Throughs/blob/main/Secret%20Recipe/Images/everything.png)
