**freeswitch log:**
94390550-8664-4e11-8879-298721842015 Dialplan: sofia/internal/1001@192.168.229.134 Regex (FAIL) [unloop] ${unroll_loops}() =~ /^true$/ break=on-false
94390550-8664-4e11-8879-298721842015 Dialplan: sofia/internal/1001@192.168.229.134 parsing [default->call_debug] continue=true
94390550-8664-4e11-8879-298721842015 Dialplan: sofia/internal/1001@192.168.229.134 Regex (FAIL) [call_debug] ${call_debug}() =~ /^true$/ break=never
94390550-8664-4e11-8879-298721842015 Dialplan: sofia/internal/1001@192.168.229.134 parsing [default->CGRateS_Auth] continue=false
94390550-8664-4e11-8879-298721842015 Dialplan: sofia/internal/1001@192.168.229.134 Regex (FAIL) [CGRateS_Auth] ${cgr_notify}(ROUTES_ERROR:SERVER_ERROR: only encoded map or array can be decoded into a struct) =~ /^$/ break=on-false
94390550-8664-4e11-8879-298721842015 Dialplan: sofia/internal/1001@192.168.229.134 parsing [default->CGRateS_AuthForbidden] continue=false
94390550-8664-4e11-8879-298721842015 Dialplan: sofia/internal/1001@192.168.229.134 Regex (FAIL) [CGRateS_AuthForbidden] ${cgr_notify}(ROUTES_ERROR:SERVER_ERROR: only encoded map or array can be decoded into a struct) =~ /^-INSUFFICIENT_FUNDS$/ break=on-false
94390550-8664-4e11-8879-298721842015 Dialplan: sofia/internal/1001@192.168.229.134 parsing [default->CGRateS_AuthForbidden] continue=false
94390550-8664-4e11-8879-298721842015 Dialplan: sofia/internal/1001@192.168.229.134 Regex (FAIL) [CGRateS_AuthForbidden] ${cgr_notify}(ROUTES_ERROR:SERVER_ERROR: only encoded map or array can be decoded into a struct) =~ /^-UNAUTHORIZED_DESTINATION$/ break=on-false
94390550-8664-4e11-8879-298721842015 Dialplan: sofia/internal/1001@192.168.229.134 parsing [default->CGRateS_Error] continue=false
94390550-8664-4e11-8879-298721842015 Dialplan: sofia/internal/1001@192.168.229.134 Regex (FAIL) [CGRateS_Error] ${cgr_notify}(ROUTES_ERROR:SERVER_ERROR: only encoded map or array can be decoded into a struct) =~ /^-SYSTEM_ERROR$/ break=on-false
94390550-8664-4e11-8879-298721842015 Dialplan: sofia/internal/1001@192.168.229.134 parsing [default->CGR Routes] continue=false
94390550-8664-4e11-8879-298721842015 Dialplan: sofia/internal/1001@192.168.229.134 Regex (FAIL) [CGR Routes] cgr_routes() =~ /.+/ break=on-false
94390550-8664-4e11-8879-298721842015 Dialplan: sofia/internal/1001@192.168.229.134 parsing [default->Local_Extension] continue=false
94390550-8664-4e11-8879-298721842015 Dialplan: sofia/internal/1001@192.168.229.134 Regex (PASS) [Local_Extension] destination_number(1003) =~ /^(10[01][0-9])$/ break=on-false
94390550-8664-4e11-8879-298721842015 Dialplan: sofia/internal/1001@192.168.229.134 Action set(ringback=${us-ring})
94390550-8664-4e11-8879-298721842015 Dialplan: sofia/internal/1001@192.168.229.134 Action set(call_timeout=30)
94390550-8664-4e11-8879-298721842015 Dialplan: sofia/internal/1001@192.168.229.134 Action bridge(user/${destination_number}@${domain_name})
94390550-8664-4e11-8879-298721842015 2023-02-22 09:49:18.374335 96.03% [DEBUG] switch_core_state_machine.c:281 (sofia/internal/1001@192.168.229.134) State Change CS_ROUTING -> CS_EXECUTE
94390550-8664-4e11-8879-298721842015 2023-02-22 09:49:18.374335 96.03% [DEBUG] switch_core_state_machine.c:640 (sofia/internal/1001@192.168.229.134) State ROUTING going to sleep
94390550-8664-4e11-8879-298721842015 2023-02-22 09:49:18.374335 96.03% [DEBUG] switch_core_state_machine.c:581 (sofia/internal/1001@192.168.229.134) Running State Change CS_EXECUTE (Cur 1 Tot 3)

///
**cgrates log:**
Feb 22 09:46:08 cgrates CGRateS <CGRFreeswitch> [7580]: <FSock> Attempt to connect to FreeSWITCH, received: dial tcp 192.168.229.134:8021: connect: connection refused
Feb 22 09:46:09 cgrates CGRateS <CGRFreeswitch> [7580]: <FSock> Attempt to connect to FreeSWITCH, received: dial tcp 192.168.229.134:8021: connect: connection refused
Feb 22 09:46:11 cgrates CGRateS <CGRFreeswitch> [7580]: <FSock> Successfully connected to FreeSWITCH!


Feb 22 09:48:15 cgrates CGRateS <CGRFreeswitch> [7580]: <FreeSWITCHAgent> cgr_flags variable is not set, using defaults
Feb 22 09:49:18 cgrates CGRateS <CGRFreeswitch> [7580]: <FreeSWITCHAgent> Could not authorize event 94390550-8664-4e11-8879-298721842015, error: ROUTES_ERROR:SERVER_ERROR: only encoded map or array can be decoded into a struct

///
**after adding cgr_flags on 1002.xml, 1001 was running out of credit, err msg misleading**:

Feb 22 11:23:29 cgrates CGRateS <CGRFreeswitch> [7580]: Destination 1001 not authorized for account: cgrates.org:1001, subject: *out:cgrates.org:call:1001
Feb 22 11:23:29 cgrates CGRateS <CGRFreeswitch> [7580]: <Rater> Error getting cost for account key <cgrates.org:1001>: UNAUTHORIZED_DESTINATION
Feb 22 11:23:29 cgrates CGRateS <CGRFreeswitch> [7580]: <FreeSWITCHAgent> Could not authorize event 2107b95b-69c8-4d12-bebf-3d74a3a20ca5, error: RALS_ERROR:UNAUTHORIZED_DESTINATION
Feb 22 11:23:29 cgrates CGRateS <CGRFreeswitch> [7580]: Destination 1001 not authorized for account: cgrates.org:1001, subject: *out:cgrates.org:call:1001
Feb 22 11:23:44 cgrates CGRateS <CGRFreeswitch> [7580]: Destination 1004 not authorized for account: cgrates.org:1001, subject: *out:cgrates.org:call:1001
Feb 22 11:23:44 cgrates CGRateS <CGRFreeswitch> [7580]: <Rater> Error getting cost for account key <cgrates.org:1001>: UNAUTHORIZED_DESTINATION
Feb 22 11:23:44 cgrates CGRateS <CGRFreeswitch> [7580]: <FreeSWITCHAgent> Could not authorize event 84954723-bb98-44f3-9c21-74ed9a31120e, error: RALS_ERROR:UNAUTHORIZED_DESTINATION
Feb 22 11:23:44 cgrates CGRateS <CGRFreeswitch> [7580]: Destination 1004 not authorized for account: cgrates.org:1001, subject: *out:cgrates.org:call:1001
Feb 22 11:25:19 cgrates CGRateS <CGRFreeswitch> [7580]: <FreeSWITCHAgent> Could not authorize event 2c6ca23f-67dc-443a-86f9-92cddf62453f, error: ROUTES_ERROR:SERVER_ERROR: only encoded map or array can be decoded into a struct
Feb 22 11:26:35 cgrates CGRateS <CGRFreeswitch> [7580]: <FSock> Successfully connected to FreeSWITCH!
////

46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:26:34.236411 97.93% [DEBUG] switch_core_media.c:8651 AUDIO RTP [sofia/internal/1001@192.168.229.1:52332] 192.168.229.134 port 24232 -> 192.168.1.2 port 4008 codec: 0 ms: 20
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:26:34.236411 97.93% [DEBUG] switch_rtp.c:4377 Starting timer [soft] 160 bytes per 20ms
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:26:34.236411 97.93% [DEBUG] switch_core_media.c:8871 Activating RTCP PORT 4009
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:26:34.236411 97.93% [DEBUG] switch_rtp.c:4708 RTCP send rate is: 1000 and packet rate is: 20000 Remote Port: 4009
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:26:34.236411 97.93% [DEBUG] switch_rtp.c:2534 Setting RTCP remote addr to 192.168.1.2:4009 2
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:26:34.236411 97.93% [DEBUG] switch_core_media.c:8963 sofia/internal/1001@192.168.229.1:52332 Set 2833 dtmf send payload to 101
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:26:34.236411 97.93% [DEBUG] switch_core_media.c:8970 sofia/internal/1001@192.168.229.1:52332 Set 2833 dtmf receive payload to 101
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:26:34.236411 97.93% [DEBUG] switch_core_media.c:8993 sofia/internal/1001@192.168.229.1:52332 Set rtp dtmf delay to 40
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:26:34.236411 97.93% [NOTICE] sofia.c:8674 Channel [sofia/internal/1001@192.168.229.1:52332] has been answered
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:26:34.236411 97.93% [DEBUG] switch_channel.c:3893 (sofia/internal/1001@192.168.229.1:52332) Callstate Change RINGING -> ACTIVE
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_core_media.c:5527 Audio Codec Compare [opus:105:48000:20:0:1]/[G722:9:8000:20:64000:1]
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_core_media.c:5527 Audio Codec Compare [opus:105:48000:20:0:1]/[PCMU:0:8000:20:64000:1]
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_core_media.c:5527 Audio Codec Compare [opus:105:48000:20:0:1]/[PCMA:8:8000:20:64000:1]
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_core_media.c:5527 Audio Codec Compare [opus:105:48000:20:0:1]/[GSM:3:8000:20:13200:1]
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_core_media.c:5527 Audio Codec Compare [G729:18:8000:20:8000:1]/[G722:9:8000:20:64000:1]
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_core_media.c:5527 Audio Codec Compare [G729:18:8000:20:8000:1]/[PCMU:0:8000:20:64000:1]
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_core_media.c:5527 Audio Codec Compare [G729:18:8000:20:8000:1]/[PCMA:8:8000:20:64000:1]
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_core_media.c:5527 Audio Codec Compare [G729:18:8000:20:8000:1]/[GSM:3:8000:20:13200:1]
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_core_media.c:5527 Audio Codec Compare [PCMU:0:8000:20:64000:1]/[G722:9:8000:20:64000:1]
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_core_media.c:5527 Audio Codec Compare [PCMU:0:8000:20:64000:1]/[PCMU:0:8000:20:64000:1]
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_core_media.c:5582 Audio Codec Compare [PCMU:0:8000:20:64000:1] ++++ is saved as a match
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_core_media.c:5527 Audio Codec Compare [PCMU:0:8000:20:64000:1]/[PCMA:8:8000:20:64000:1]
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_core_media.c:5527 Audio Codec Compare [PCMU:0:8000:20:64000:1]/[GSM:3:8000:20:13200:1]
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_core_media.c:5527 Audio Codec Compare [PCMA:8:8000:20:64000:1]/[G722:9:8000:20:64000:1]
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_core_media.c:5527 Audio Codec Compare [PCMA:8:8000:20:64000:1]/[PCMU:0:8000:20:64000:1]
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_core_media.c:5527 Audio Codec Compare [PCMA:8:8000:20:64000:1]/[PCMA:8:8000:20:64000:1]
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_core_media.c:5582 Audio Codec Compare [PCMA:8:8000:20:64000:1] ++++ is saved as a match
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_core_media.c:5527 Audio Codec Compare [PCMA:8:8000:20:64000:1]/[GSM:3:8000:20:13200:1]
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_core_media.c:5443 Set telephone-event payload to 101@8000
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_core_media.c:3750 Set Codec sofia/internal/1003@192.168.229.134 PCMU/8000 20 ms 160 samples 64000 bits 1 channels
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_core_codec.c:111 sofia/internal/1003@192.168.229.134 Original read codec set to PCMU:0
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_core_media.c:5792 Set telephone-event payload to 101@8000
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_core_media.c:5850 sofia/internal/1003@192.168.229.134 Set 2833 dtmf send payload to 101 recv payload to 101
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_core_media.c:8651 AUDIO RTP [sofia/internal/1003@192.168.229.134] 192.168.229.134 port 30028 -> 192.168.1.2 port 20064 codec: 0 ms: 20
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_rtp.c:4377 Starting timer [soft] 160 bytes per 20ms
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_core_media.c:8963 sofia/internal/1003@192.168.229.134 Set 2833 dtmf send payload to 101
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_core_media.c:8970 sofia/internal/1003@192.168.229.134 Set 2833 dtmf receive payload to 101
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_core_media.c:8993 sofia/internal/1003@192.168.229.134 Set rtp dtmf delay to 40
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [NOTICE] sofia_media.c:90 Pre-Answer sofia/internal/1003@192.168.229.134!
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_channel.c:3566 (sofia/internal/1003@192.168.229.134) Callstate Change RINGING -> EARLY
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] switch_core_media.c:8633 Audio params are unchanged for sofia/internal/1003@192.168.229.134.
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] mod_sofia.c:913 Local SDP sofia/internal/1003@192.168.229.134:
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 v=0
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 o=FreeSWITCH 1677024366 1677024367 IN IP4 192.168.229.134
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 s=FreeSWITCH
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 c=IN IP4 192.168.229.134
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 t=0 0
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 m=audio 30028 RTP/AVP 0 101
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 a=rtpmap:0 PCMU/8000
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 a=rtpmap:101 telephone-event/8000
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 a=fmtp:101 0-15
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 a=ptime:20
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 a=sendrecv
2d07b3fd-ed4b-4746-ad93-270c9fb4f301
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.253791 97.93% [DEBUG] sofia.c:7487 Channel sofia/internal/1003@192.168.229.134 entering state [completed][200]
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.275089 97.93% [NOTICE] switch_ivr_originate.c:3844 Channel [sofia/internal/1003@192.168.229.134] has been answered
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.275089 97.93% [DEBUG] switch_channel.c:3893 (sofia/internal/1003@192.168.229.134) Callstate Change EARLY -> ACTIVE
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.275089 97.93% [DEBUG] switch_ivr_originate.c:3902 Originate Resulted in Success: [sofia/internal/1001@192.168.229.1:52332] Peer UUID: 46315daa-95ab-4fe6-a7d6-85ee66a179c6
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.275089 97.93% [DEBUG] switch_ivr_originate.c:3902 Originate Resulted in Success: [sofia/internal/1001@192.168.229.1:52332] Peer UUID: 46315daa-95ab-4fe6-a7d6-85ee66a179c6
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:26:34.275089 97.93% [DEBUG] switch_ivr_bridge.c:1791 (sofia/internal/1001@192.168.229.1:52332) State Change CS_CONSUME_MEDIA -> CS_EXCHANGE_MEDIA
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:26:34.275089 97.93% [DEBUG] switch_core_state_machine.c:581 (sofia/internal/1001@192.168.229.1:52332) Running State Change CS_EXCHANGE_MEDIA (Cur 2 Tot 9)
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:26:34.275089 97.93% [DEBUG] switch_core_state_machine.c:650 (sofia/internal/1001@192.168.229.1:52332) State EXCHANGE_MEDIA
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:26:34.275089 97.93% [DEBUG] mod_sofia.c:671 SOFIA EXCHANGE_MEDIA
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:26:34.275089 97.93% [DEBUG] switch_rtp.c:1775 rtcp_stats_init: audio ssrc[1350046849] base_seq[1207]
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:26:34.275089 97.93% [DEBUG] sofia.c:7487 Channel sofia/internal/1003@192.168.229.134 entering state [ready][200]
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:26:34.493982 97.93% [INFO] switch_rtp.c:7471 Auto Changing audio port from 192.168.1.2:4008 to 192.168.229.1:4008
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:26:39.574285 96.30% [INFO] switch_rtp.c:6917 Auto Changing audio RTCP port from 192.168.1.2:4009 to 192.168.229.1:4009
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:26:39.574285 96.30% [DEBUG] switch_rtp.c:2534 Setting RTCP remote addr to 192.168.229.1:4009 2
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:27:03.017762 93.57% [NOTICE] sofia.c:1065 Hangup sofia/internal/1001@192.168.229.1:52332 [CS_EXCHANGE_MEDIA] [NORMAL_CLEARING]
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_ivr_bridge.c:911 BRIDGE THREAD DONE [sofia/internal/1001@192.168.229.1:52332]
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:650 (sofia/internal/1001@192.168.229.1:52332) State EXCHANGE_MEDIA going to sleep
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:581 (sofia/internal/1001@192.168.229.1:52332) Running State Change CS_HANGUP (Cur 2 Tot 9)
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:844 (sofia/internal/1001@192.168.229.1:52332) Callstate Change ACTIVE -> HANGUP
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:846 (sofia/internal/1001@192.168.229.1:52332) State HANGUP
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:27:03.017762 93.57% [DEBUG] mod_sofia.c:468 Channel sofia/internal/1001@192.168.229.1:52332 hanging up, cause: NORMAL_CLEARING
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:59 sofia/internal/1001@192.168.229.1:52332 Standard HANGUP, cause: NORMAL_CLEARING
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:846 (sofia/internal/1001@192.168.229.1:52332) State HANGUP going to sleep
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:616 (sofia/internal/1001@192.168.229.1:52332) State Change CS_HANGUP -> CS_REPORTING
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:581 (sofia/internal/1001@192.168.229.1:52332) Running State Change CS_REPORTING (Cur 2 Tot 9)
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:932 (sofia/internal/1001@192.168.229.1:52332) State REPORTING
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:168 sofia/internal/1001@192.168.229.1:52332 Standard REPORTING, cause: NORMAL_CLEARING
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:932 (sofia/internal/1001@192.168.229.1:52332) State REPORTING going to sleep
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:607 (sofia/internal/1001@192.168.229.1:52332) State Change CS_REPORTING -> CS_DESTROY
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_session.c:1743 Session 9 (sofia/internal/1001@192.168.229.1:52332) Locked, Waiting on external entities
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_ivr_bridge.c:911 BRIDGE THREAD DONE [sofia/internal/1003@192.168.229.134]
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:27:03.017762 93.57% [NOTICE] switch_core_state_machine.c:382 sofia/internal/1003@192.168.229.134 has executed the last dialplan instruction, hanging up.
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:27:03.017762 93.57% [NOTICE] switch_core_state_machine.c:384 Hangup sofia/internal/1003@192.168.229.134 [CS_EXECUTE] [NORMAL_CLEARING]
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:647 (sofia/internal/1003@192.168.229.134) State EXECUTE going to sleep
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:581 (sofia/internal/1003@192.168.229.134) Running State Change CS_HANGUP (Cur 2 Tot 9)
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:844 (sofia/internal/1003@192.168.229.134) Callstate Change ACTIVE -> HANGUP
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:846 (sofia/internal/1003@192.168.229.134) State HANGUP
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:27:03.017762 93.57% [DEBUG] mod_sofia.c:462 sofia/internal/1003@192.168.229.134 Overriding SIP cause 480 with 200 from the other leg
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:27:03.017762 93.57% [DEBUG] mod_sofia.c:468 Channel sofia/internal/1003@192.168.229.134 hanging up, cause: NORMAL_CLEARING
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:27:03.017762 93.57% [DEBUG] mod_sofia.c:522 Sending BYE to sofia/internal/1003@192.168.229.134
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:59 sofia/internal/1003@192.168.229.134 Standard HANGUP, cause: NORMAL_CLEARING
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:846 (sofia/internal/1003@192.168.229.134) State HANGUP going to sleep
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:616 (sofia/internal/1003@192.168.229.134) State Change CS_HANGUP -> CS_REPORTING
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:581 (sofia/internal/1003@192.168.229.134) Running State Change CS_REPORTING (Cur 2 Tot 9)
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:932 (sofia/internal/1003@192.168.229.134) State REPORTING
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:168 sofia/internal/1003@192.168.229.134 Standard REPORTING, cause: NORMAL_CLEARING
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:932 (sofia/internal/1003@192.168.229.134) State REPORTING going to sleep
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:607 (sofia/internal/1003@192.168.229.134) State Change CS_REPORTING -> CS_DESTROY
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_session.c:1743 Session 8 (sofia/internal/1003@192.168.229.134) Locked, Waiting on external entities
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:27:03.017762 93.57% [NOTICE] switch_core_session.c:1761 Session 8 (sofia/internal/1003@192.168.229.134) Ended
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:27:03.017762 93.57% [NOTICE] switch_core_session.c:1765 Close Channel sofia/internal/1003@192.168.229.134 [CS_DESTROY]
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:735 (sofia/internal/1003@192.168.229.134) Running State Change CS_DESTROY (Cur 1 Tot 9)
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:745 (sofia/internal/1003@192.168.229.134) State DESTROY
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:27:03.017762 93.57% [DEBUG] mod_sofia.c:379 sofia/internal/1003@192.168.229.134 SOFIA DESTROY
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:175 sofia/internal/1003@192.168.229.134 Standard DESTROY
2d07b3fd-ed4b-4746-ad93-270c9fb4f301 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:745 (sofia/internal/1003@192.168.229.134) State DESTROY going to sleep
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:27:03.017762 93.57% [NOTICE] switch_core_session.c:1761 Session 9 (sofia/internal/1001@192.168.229.1:52332) Ended
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:27:03.017762 93.57% [NOTICE] switch_core_session.c:1765 Close Channel sofia/internal/1001@192.168.229.1:52332 [CS_DESTROY]
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:735 (sofia/internal/1001@192.168.229.1:52332) Running State Change CS_DESTROY (Cur 0 Tot 9)
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:745 (sofia/internal/1001@192.168.229.1:52332) State DESTROY
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:27:03.017762 93.57% [DEBUG] mod_sofia.c:379 sofia/internal/1001@192.168.229.1:52332 SOFIA DESTROY
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:175 sofia/internal/1001@192.168.229.1:52332 Standard DESTROY
46315daa-95ab-4fe6-a7d6-85ee66a179c6 2023-02-22 11:27:03.017762 93.57% [DEBUG] switch_core_state_machine.c:745 (sofia/internal/1001@192.168.229.1:52332) State DESTROY going to sleep
2023-02-22 11:27:03.316523 93.57% [DEBUG] switch_scheduler.c:147 Deleting task 5 switch_ivr_schedule_broadcast (2d07b3fd-ed4b-4746-ad93-270c9fb4f301)


////






**note from**: https://github.com/cgrates/cgrates/issues/1461

   <extension name="CGRateS_Auth">
     <condition field="${cgr_notify}" expression="^$">
        <action application="set" data="cgr_reqtype=*prepaid" />
        <action application="set" data="cgr_category=XXX" />
        <action application="set" data="cgr_subsystems=*resources;*attributes;*sessions;*suppliers;*thresholds;*stats;*accounts" />
        <action application="park" />
      </condition>
    </extension>
    <extension name="CGRateS_AuthForbidden">
      <condition field="${cgr_notify}" expression="RALS_ERROR:ACCOUNT_NOT_FOUND">
        <action application="set" data="sip_rh_X-SureVoIP-Reject-Reason=${cgr_notify}" />    
        <action application="hangup" data="CALL_REJECTED" />
      </condition>
    </extension>
    <extension name="CGRateS_RatingPlanNotFound">
      <condition field="${cgr_notify}" expression="RALS_ERROR:RATING_PLAN_NOT_FOUND">
        <action application="set" data="sip_rh_X-SureVoIP-Reject-Reason=${cgr_notify}" />    
        <action application="hangup" data="CALL_REJECTED" />
      </condition>
    </extension>
    <extension name="CGRateS_Error">
      <condition field="${cgr_notify}" expression="RALS_ERROR:.+">
        <action application="set" data="sip_rh_X-SureVoIP-Reject-Reason=${cgr_notify}" />    
        <action application="hangup" data="NORMAL_TEMPORARY_FAILURE" />
      </condition>
    </extension>
...
    <extension name="catchall" continue="true">
    <!-- If none of the above matches then cease this call immediately -->
        <condition break="always">
            <action application="set" data="sip_rh_X-SureVoIP-Reject-Reason=CATCH_ALL" />    
            <action application="hangup" data="NORMAL_TEMPORARY_FAILURE"/>  
        </condition>
    </extension>