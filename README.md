I start off with commandline arguments parsing as well as the url parsing. Then the client will initialize a socket connection to the server, after receiving a hello message, the client will request to establish a ssl connection with the server and then send the user/password information.
After successfully login in, the client will send PBSZ/PROT/TYPE/MODE/STRU request so make sure everything is ready before the actual operation to be conducted.

Next, I implement the mkdir/rmdir/rm/ls operation, which is quite easy. The ls operation requires an 
additional data channel which could be done by sending PASV, then parse the server's response to get a new ip/port and then warp them with tls. 

Next is the cp and rm operation, these two operations are quite 2-in-1, once cp is implemented correctly, all I need to do with rm is to delete the origin file after the copy is done. There are two different situations, one is copy from remote server to local and another vice versa. To copy from the remote server, the client send a retr message to the server and then open up a tls data channel for transporting, after receiving the data, write it to the local file. To send to a remote server, the client reads data from the local file and then send through tls channel to the server.

The biggest problem I encountered is in the process of sending from local to remote, at first I mistakenly implemented the STOR_msg to contain the local file path, the error is very nature because in normal thinking we need to contain the file path if we want to send it. I didn't fix the issue until I found that I have a send function which also contains the local file path(args.params[0]), why would I have to send it twice? Meanwhile, I started to look for the remote path in the implementation, because no data transportation could be made without that, but I found none, which makes me realize that the STOR_msg should instead contain the remote file path. 

Tests:
I test my code by run rm/rmdir/mkdir/cp/mv through commandline and use ls to check if they have the correct result on the remote server and all of them work properly.
 