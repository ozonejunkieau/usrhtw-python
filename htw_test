# -*- coding: utf-8 -*-

import socket
import httplib, urllib


TCP_ADDR = 'x.x.x.x'
TCP_PORT = 8899

PACK_LEN = 11


API_KEY = 'xxxx'




s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

s.settimeout(30)

while True: #keep trying forever
    
    try:
        s.close()
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.settimeout(30)
        s.connect((TCP_ADDR, TCP_PORT))
        
        while True:
            bytes_data = zeros(PACK_LEN,int)
            str_data = s.recv(PACK_LEN) #this should probably have a timeout
            hex_data = str_data.encode('hex')
            if len(hex_data)<>22:
                hex_data = '0000000000000000000011'
                raise Exception("Error reading data")
            
            for n in range(0,PACK_LEN): #convert to array of bytes
                lower = 2*n
                upper = lower + 2
                bytes_data[n] = int(hex_data[lower:upper],16)
            
        
            humid =  (((bytes_data[6])<<8)+(bytes_data[7]))/10.0
            temp =  (((((bytes_data[8])&0x7F)<<8)+(bytes_data[9]))/10.0)
            
            if int(bytes_data[8]) & 0x80: #invert temp if sign bit is set
                temp = -1.0* temp
                
            checksum = (uint(sum(bytes_data[0:10])) & 0xFF)+1
            
            if checksum == bytes_data[10]:
                print "Valid!"
                print "Temp: " + str(temp)
                print "Hum: " + str(humid)
                
                params = urllib.urlencode({'field1': temp, 'field2': humid,'key':API_KEY})
                headers = {"Content-type": "application/x-www-form-urlencoded","Accept": "text/plain"}
                conn = httplib.HTTPConnection("api.thingspeak.com:80")
                conn.request("POST", "/update", params, headers)
                response = conn.getresponse()
                print response.status, response.reason
                data = response.read()
                conn.close()        
                
                
            else:
                print "Invalid. :("
    except:
        print "Error ocurred, restarting"
        
