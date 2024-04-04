XUI.ONE is the most famous paid IPTV panel provided by GTA and team with great features and improved security. The panel kept available with 2 of the official versions and a beta version all the time. The panel can be installed both on Ubuntu 20 and Ubuntu 18, but LBs seems to be done over Ubuntu 20 only.

Basics for XUI.ONE​

Service Commands
service xuione <start|stop|reload|restart>
Example: service xuione start

Generate License
After a new installation, the license will be generated from the setup page. This can be access via: http://yoursite.com:8080/accesscode
Each installation has a unique access code, if yours for some reason doesn’t work, try the Access Code instructions below.

If you’ve already installed and progressed past the setup page, but need to regenerate the license, or on a load balancer for example, try the below instructions:

    Visit the XUI Billing Panel, log in and Reissue your license (for Main server only). Load balancers can skip this step.
    Ensure your license number is in /home/xui/config/config.ini
    Run the following commands on the server via SSH:
    rm /home/xui/config/license
    sudo -u xui /home/xui/bin/php/bin/php /home/xui/crons/license.php

The license.php will output information about your license and whether it is valid or not. If this doesn’t work, and your license is definitely in the Reissued state, then you’ll have to contact support or post on the forum.

No Access Code
If you can’t access the admin interface, as you don’t have an access code or the access code you have doesn’t work, check /home/xui/bin/nginx/conf/codes/ to ensure there is a code that ends with .conf. If there is, try using this as your access code.

If there are no codes that end with .conf, or the ones that do are not for an admin interface login, then copy template to admin.conf and make the following changes:

    Replace #CODE# with admin
    Replace #TYPE# with admin
    Delete #WHITELIST#
    Enter mysql via your preferred method and run: USE `xui`; INSERT INTO `access_codes`( `code`, `type`, `enabled`, `groups`) VALUES ('admin', 0, 1, '[1]');
    Run the following command via SSH: service xuione reload
    You should now be able to access the admin panel via: http://yoursite.com:8080/admin

Login Error: “You do not have access”
This error relates to the access code specifically, it means the access code you’re using is for a group that your user is not in. Make sure you’re using the right user, or the right access code. If you can’t access the panel because of this then use the instructions from the No Access Code section to create a new access code for admins.

MySQL Access Denied
If you’re getting access denied on the main server, you’re going to need to edit /home/xui/config/config.ini and change the username and password in the Encrypted section to match the credentials you were given when installing XUI, like this:

[Encrypted]
username = "username"
password = "password"

After saving, you can restart the panel and see if this has worked.

For load balancers, you can either follow the above steps or ensure that the main panel mysql is working then reinstall the laod balancer. There is also an option in Management -> Tools -> Quick Tools -> General that will regenerate mysql authentication for your load balancers. If this doesn’t fix the issue, the XUI mysql user doesn’t have permission to authorise external users. In which case you will have to ensure that the user does by granting the privilege, or allowing the load balancer IP manually. Either worst-case option here is an advanced procedure and may require external help.

Load Balancer Offline
Access the load balancer via SSH and run /etc/init.d/xuione stop then /etc/init.d/xuione start, this will tell you some information about what is wrong with the installation that prevents it from going online, it could be a mysql issue, it could be a license issue, or it could be something relating to libraries not being installed. Follow the trail.

Migration
In order to migrate your old database to XUI, a separate database has been created called xui_migrate which XUI has full access to. You will need to restore your migration SQL file to this database in order for migration to work.

You can restore the SQL file using the following shell command:
mysql xui_migrate < database.sql

You can migrate at any time by visiting: http://yoursite.com:8080/accesscode/setup

You can also manually migrate via SSH using the following command:
/home/xui/bin/php/bin/php /home/xui/includes/cli/migrate.php

If a failed migration causes issues with logging in, you can restore a blank database using the following command:
mysql xui < /home/xui/bin/install/database.sql

Reset Admin User
If you need to reset the admin user for any reason, you can run the following via mysql to restore user ID 1 to the username admin and password admin. Please change the username and password afterwards.

USE `xui`; REPLACE INTO `users`(`id`, `username`, `password`, `member_group_id`, `status`, `owner_id`, `date_registered`) VALUES(1, 'admin', '$6$rounds=20000$xui$eQfRsD2gsIUhoY5RnnYN82qiB5VeZTcHICQrFxXpa98J2R1454b6lzVHVjiJ.NP0gi0X3K7NXVgxeR1VhVhg61', 1, 1, 0, UNIX_TIMESTAMP());

XUI.ONE Admin API – Placeholder​

You can then access the API via:
http://yoursite.com:8080/accesscode/?api_key=#APIKEY#

For Example:
https://[domain]:[port]/[accesscode]/?api_key=[api_key]&actoion=[api_command]
https://bestdomain.com:9000/rnVKrSLe/?api_key=1A2C5C80056A80F5AB6ECAD3937875DE&actoion=packages

As a developer, use this as your API endpoint and set the "action" parameter to one of the following API commands:

GET INFO
mysql_query
user_info
get_lines
get_mags
get_enigmas
get_users
get_streams
get_channels
get_stations
get_movies
get_series_list
get_episodes

LOGS & EVENT API
activity_logs
live_connections
credit_logs
client_logs
user_logs
stream_errors
watch_output
system_logs
login_logs
restream_logs
mag_events

LINE API
get_line
create_line
edit_line
delete_line
disable_line
enable_line
unban_line
ban_line

USER API
get_user
create_user
edit_user
delete_user
disable_user
enable_user

MAG API
get_mag
create_mag
edit_mag
delete_mag
disable_mag
enable_mag
unban_mag
ban_mag
convert_mag

ENIGMA API
get_enigma
create_enigma
edit_enigma
delete_enigma
disable_enigma
enable_enigma
unban_enigma
ban_enigma
convert_enigma

BOUQUETE API
get_bouquets
get_bouquet
create_bouquet
edit_bouquet
delete_bouquet

ACCESS CODE API
get_access_codes
get_access_code
create_access_code
edit_access_code
delete_access_code

HMAC API
get_hmacs
get_hmac
create_hmac
edit_hmac
delete_hmac

EPG API
get_epgs
get_epg
create_epg
edit_epg
delete_epg
reload_epg

GROUPS API
get_groups
get_group
create_group
edit_group
delete_group

PACKAGES API
get_packages
get_package
create_package
edit_package
delete_package

TRANSCODING PROFILE API
get_transcode_profiles
get_transcode_profile
create_transcode_profile
edit_transcode_profile
delete_transcode_profile

RTMP API
get_rtmp_ips
get_rtmp_ip
create_rtmp_ip
edit_rtmp_ip
delete_rtmp_ip

CATEGORIES API
get_categories
get_category
create_category
edit_category
delete_category

SUB RESELLER API
get_subresellers
get_subreseller
create_subreseller
edit_subreseller
delete_subreseller

WATCH FOLDERS API
get_watch_folders
get_watch_folder
create_watch_folder
edit_watch_folder
delete_watch_folder
reload_watch_folder

BLOCKED ISP & IP API
get_blocked_isps
add_blocked_isp
delete_blocked_isp
get_blocked_uas
add_blocked_ua
delete_blocked_ua
get_blocked_ips
add_blocked_ip
delete_blocked_ip
flush_blocked_ips

STREAMS API
get_stream
create_stream
edit_stream
start_stream
stop_stream
delete_stream

CHANNEL API
get_channel
create_channel
edit_channel
start_channel
stop_channel
delete_channel

STATION API
get_station
create_station
edit_station
start_station
stop_station
delete_station

MOVIE API
get_movie
create_movie
edit_movie
start_movie
stop_movie
delete_movie

SERIES API
get_series
create_series
edit_series
delete_series

EPISONE API
get_episode
create_episode
delete_episode
edit_episode
start_episode
stop_episode

SERVERS API
get_servers
get_server
install_server
delete_server
edit_server
install_proxy
edit_proxy

SETTINGS, STATS AND OTHER API
get_settings
edit_settings
get_server_stats
get_fpm_status
get_rtmp_stats
get_free_space
get_pids
get_certificate_info
reload_nginx
clear_temp
clear_streams
update_blacklist
get_directory
kill_pid
kill_connection
reload_cache

Upcoming API information will help identify what each function does, and what parameters can be submitted.
For now, if you want to poke around I suggest using the Admin Interface with Developer Tools enabled and log any traffic to post.php. The parameters sent there will be the same parameters you can send to the API function it relates to.

For example if you go to Streams and add a stream, you can log post.php and submit the same request to the create_stream function to replicate it.
To edit a stream, pass the id with it too.

Some of the functions are server specific, so send server_id with them, for example:
get_server_stats, get_fpm_status, get_rtmp_stats, get_free_space, get_pids, get_certificate_info, reload_nginx, clear_temp, clear_streams, update_blacklist, get_directory

Line Example – Editing line #4 to change password to helloworld and select bouquets 1 and 2:
action=edit_line&id=4&username=test&password=helloworld&bouquets_selected[]=1&bouquets_selected[]=2

 

HowTo push a RTMP stream to XUI.ONE​

 

1.1 Go to the Server Settings >> Manage Servers
1.2 Open the Server Settings under Edit Server
1.3 Open the Tap “Advanced” and check out the RTMP Port

1.4 Go to Management >> Acccess Control >> RTMP IPs
1.5 We have to add here 2 IPs (One for Device where RTMP stream comes from and the other one must be the Local IP 127.0.0.1)
In my case will use OBS for the RTMP stream for Demo purposes, so i will add my public IP with PUSH permissions (I am pushing the stream to the server)
The addtional Local IP 127.0.0.1 with pull permissions is required to pull my incoming RTMP stream to the Panel it self as a Channel.

1.6 Go to Content >> Streams >> Add Stream
1.7 Add your decided Stream Name (in my case RTMP), select your decided Cateory and Bouguet
1.8 Add the source stream URL, like: rtmp://localhost:8880/live/mychannel?password=h2WVR4j2SSgXZ46t
> mychannel (this is the stream key and can be freely chosen)
> ?password=h2WVR4j2SSgXZ46t (The RTMP access password from step 1.5)

1.9 Under Servers Tap, select the Server(s) (Main or LB) where you want to stream from this Channel and save the Channel

2. Push your RTMP stream from your PC or Encoder to the Panel

2.1 Open OBS Settings >> Stream and the Server IP & Port and RTMP credentials like:

Server: rtmp://[SERVERIP}:[RTMPPORT]/live
Stream Key: [CHANNELNAME]?password=[RTMPPASSWORD]

In my example it looks like:

2.2 Push the RTMP stream to the Server (XUI.ONE Panel) with clicking the Start Stream button. If you succesfull conected you will see on the rigt buttom the FPS and Bitrate.

2.3 Go back to the Panel >> Content >> Manage Streams and check the Channel you added at step 1.6 – 1.9
If the Channel Uptime has been started, you can be happy !

 

HowTo add Admin API in XUI.ONE​

1. Open the Panel, go to MANAGEMENT >> ACCESS CONTROL >> ACCESS CODES
2. Add Access Code, select from ACCESS TYPE: Admin API and click next

3. Select as Group “Administrators”

4. If you want restrict the API to a cretain IP, add the IP and save it

5. To get the Admin API Key, open the Admin Profile
Migrate DB from XTREAMUI to XUI.ONE

Creditos: https://iptvblog.org
