# Setting up VS Code for Hydra Connection

Instructions for using VS Code to connect to Hydra via SSH. This connection type requires a computer on the Smithsonian network or VPN connection.

First step is to install VS Code. Go to <https://code.visualstudio.com/> and click on the download link.

Once it is installed open the program and click on the Extension button on the left side.

![VS Code Extension Button](images/extension_button.png)

Then search for the word "remote" to pull up a bunch of results. Click Install on the first option, which should be "Remote Development", which is an extension pack that will install multiple extensions published by Microsoft.

![VS Code Extension Search Remote](images/remote_search.png)

If this worked, it will show that multiple extensions are now installed.

![VS Code Remote Extensions Installed](images/remote_installed.png)

Now click on the new Remote Explorer icon on the left sidebar. Hover over the "SSH" section and then click on the + icon to add an SSH connection to Hydra.

![VS Code Remote Panel Blank](images/remote_panel.png)

A little panel will show up in the top middle of VS Code with an example SSH connection command. Fill it in with `ssh USER@hydra-login01.si.edu`.

![VS Code Add SSH](images/add_ssh.png)

You will then get a follow-up prompt that asks where to store this config. Choose the first option. Windows: `C:\Users\USER\.ssh\config`, Mac: `/Users/USER/.ssh/config`

*Windows*
![VS Code Add SSH Config (Windows)](images/add_ssh_config_win.png)
*Mac*
![VS Code Add SSH Config (Mac)](images/add_ssh_config_mac.png)

It will do a little bit of thinking, but then you should see the Hydra SSH connection added to your Remote Explorer tab.

Click on the icon either for "Connect in Current Window" or "Connect in New Window" to connect to Hydra. It doesn't really matter which one you choose.

![VS Code Remote Panel with Hydra](images/remote_panel_hydra.png)

You may get a warning about the SSH fingerprint that is displayed in the top middle of the VS Code window. If so, click "Continue".

![VS Code prompt about unknown fingerprint](images/remote_fingerprint.png)

It will again open up a little prompt in the top middle of VS Code, which asks for your Hydra password. Enter it here, and press enter.

![VS Code Hydra Password Prompt](images/hydra_password_prompt.png)

If this is the first time using VS Code on Hydra you may be prompted about the OS of the remote system, choose "Linux." It will take a while to install VS Code helper files on Hydra in your space. But when it finishes, you should see the options to Open Folder, and it will also open up a Terminal window for you.

![VS Code Remote Open Folder](images/remote_open_folder.png)

Here is an example screenshot of a home folder where we've clicked on a random file that shows how it opens it up in the Editor pane. There's also a command in the Terminal at that bottom, that shows that the path between the Explorer pane and the terminal location can get slightly disconnected.

![VS Code Remote Full Window](images/remote_full_window.png)
