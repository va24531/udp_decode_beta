# udp_decode_beta
BETA - UDP Decode, Heartbeat, Status and Message - Code Snippets


----------------------------------------------------------------------------
This is part of a larger project, and as such, this is not the entire code.
This only contains code snippets and 1 actual flow to parse certain UDP
data and forward it on to other flows.
----------------------------------------------------------------------------

This code is not 'complete'. Since there is potential to do mischevious
activities with this code, it is not posted as a complete project.

Before reinventing the wheel, I had spent days looking over other peoples
code to decode the UDP packets. I found tons of Python code. I found a few 
examples in .NET. I could not find any information on where someone had 
specifically used Javascript (and certainly not Node-Red) to decode and
format the messages. After spending days RevEng'ing the data, it was clear
to me why no had done it, it was a pain in the A**. Javascript certainly
isn't the best platform to handle converting hex packets into int16/int32
datagrams.

I am currently using 3 of the packet types, 0, 1, and 2. There are several
others, however they were of no use to me for the information I was seeking.

* Heartbeat Packet, type 0:
    MagicPacket
    Max Schema Number
    Version
    Software Application Name (Sender)
  
 This message is intended to be used by servers to detect the presence
 of a  client and also  the unexpected disappearance of  a client
 and  by clients  to learn  the schema  negotiated by  the server
 after it receives  the initial heartbeat message  from a client.
 
 
* Status Packet, type 1:
    * Dial Frequency and Mode are the only ones decoded at this time.
    
    -> DX Call, Report, TX Mode, TX Enable, Transmitting, Decoding, Rx DF,
    -> Tx DF, DE Call, DE Grid, DX Grid, TX Watchdog, Sub-Mode, Fast Mode
    
    I don't have a need for the rest of the packet data, only the dial frequency
    and mode, since I'm using this in a RX Only setup. If there is a request to
    break out the rest of the information, I may get around to do that in the future.
    
* Decode Packet, type 2

      This is the one most people are interested in, and is fully decoded
      
      New Contact     ( Bool 0/1 )
      Time            ( milliseconds since midnight UTC)
      SNR             ( Received signal strength )
      Delta Time      ( TX Station Time offset )
      Delta Freq      ( TX Station Freq offset )
      Mode            ( FT8/FT4/ )
      Message         ( Decoded Message )
      Low Confidence  ( Bool 0/1 )
      Offline Decode  ( Bool 0/1 )
 
 
 
 
 Snippets :
    
                Decode_UDP_NR       <-> Complete flow for UDP packet, decoding type, and parsing messages.
                                    <-> Copy entire code and import into Node-Red.
    
    
    UDP Packet Code
 
                heartbeat_js_nr     Javascript code to decode full type 0 UDP Packet (Heartbeat)
                status_js_nr        Javascript code to decode partial type 1 UDP Packet (Status)
                message_js_nr       Javascript code to decode full type 2 UDP Packet (Message)
                
    
    Message Decode Parsing
    
                post_processing_start       returns 'calling_cq', 'in_qso', or 'calling_station' based on message content
                post_calling_cq_js_nr       parses and formats message based on 'tx6' or includes a 'CQ' in msg.
                post_calling_station_js_nr  parses and formats message based on 'tx1' or included gridsquare
                post_in_qso_js_nr           parses and formats message based on 'tx2, 3, 4, or 5' status
                

* RR73 messages are handled as 'in_qso' format. Technically, that could be a 'calling_station' format, but the odds are extremely low that someone is actually on a boat or plane in Grid Square 'RR73', which is a pretty remote location. On the other hand ... DXpedition Anyone???
