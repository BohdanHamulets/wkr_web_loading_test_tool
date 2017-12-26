# wkr_web_loading_test_tool
This is a repository with details and instruction steps on wrk test load tool, 
which generate a lot of http and https traffic to test the web proxy


It has helped me to generate hundreds of GD reprotnig data on the proxy,
so it is very fast and cool.

Later I'll add all the needed details

Steps to configure loading
You will need 1 devbox for each WSA. I will describe setup for 1 SMA, 1WSA and 1 devbox, you can expand this steps to more machines easily with secondaryconfig command.
1. Setup Nginx on devbox
1.1. SSH to your devbox (as root)
1.2. Make sure that FreeBSD version > 9.2
1.3. cd /usr/ports/www/nginx
1.4. make install clean
1.5. cd /usr/local/etc/nginx/
1.6. Replace nginx.conf with this config.
1.7. sysrc nginx_enable=yes
1.8. service nginx start
2. Configure WSA to report to SMA
3. Setup WSA to use devbox as upstream proxy
4. Run WRK loader
4.1. Download this archive.
4.2. Create folder /data/tmp/work
4.3. SCP wrk.tar.gz from local machine to WSA:/data/tmp/work
4.4. cd /data/tmp/work/ && tar -xf wrk.tar.gz
4.5. If WSA is physical machine (-d100h means that process will be running for 100 hours. Change it, if you want): 
cd /data/tmp/work/wrk && nohup printf "\nLoad started at `date`\n" >> wrk.log && ./wrk -t5 -c10000 -d100h -s via_proxy.lua http://localhost:80/ >>& wrk.log &

               If WSA is virtual machine: 
cd /data/tmp/work/wrk && nohup printf "\nLoad started at `date`\n" >> wrk.log && ./wrk -t2 -c1000 -d100h -s via_proxy.lua http://localhost:80/ >>& wrk.log &
 
Note You really should try different -t (threads) -c (concurrent connections) options to find the option which suites you best.
4.6. If you want to stop the process or if you disconnected from the session and connected again determine PID with `ps aux | grep wrk` and then do kill -2 <PID> twice. Statistical info of wrk should be available in /data/tmp/work/wrk.log. Sometimes you may need to do kill -2 to stop the process. If you use some other signal with kill command (like kill -9) you'll loose wrk statistical output. kill -2 is like pressing Ctrl - C in interactive app.
  
More info on general usage of this tool is here: github.com/wg/wrk
