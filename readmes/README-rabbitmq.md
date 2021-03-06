

DE.SETF.AMQP: interoperation with RabbitMQ
-------

### Installation

 Get the RabbitMQ server. The [instructions](http://www.rabbitmq.com/macports.html) for OS X suggest to use macports.
 Add `http://www.rabbitmq.com/releases/macports` and sync as follows

    $ sudo emacs /opt/local/etc/macports/sources.conf
    $ sudo port sync
    $ sudo port install rabbitmq-server

 The sync succeeded, but the build did not.

    $ sudo port install rabbitmq-server
      Error: Unable to open port: can't read "proposedpath": no such variable
    $

 Oops. Turns out, the http port repository is a [problem](http://trac.macports.org/ticket/19338) with any macports < 1.8. 
Selfupdate = 1.7.10 -> 1.8.0. Ok. 
Try again. It now shows that it wants to install erlang. Despite a local build of 12B5. port reports that 13B_4 is 'active'. Ok.
In the process, `beam.smp` spent about fifteen minutes exercising the disk before I told it to stop.
The [net](http://www.digitalsanctum.com/2009/10/01/installing-erlang-on-mac-os-x/) suggested, that it should work, so try an erlang update 'by hand'.
With the most recent [erlang](http://www.erlang.org/download/otp_src_R13B03.tar.gz). 

    $ cd /Development/Applications/otp_src_R13B03
    $ ./configure
       ...
       fop is missing.
       The documentation can not be built.
    $

  Ok. One trusts, as it figured that out, it configured itself appropriately. In any event, a minute into the process, the disk activity
set in. `erlc ...`. Apparently, that's just the way erlang compiler sounds. This time it went on for just about twenty minutes before the
cascade of (much quieter) gcc activity set on. Only to resume five minutes later. In the end, thirty minutes of disk exercises later, erlang
is installed.

 Now, is macports happy? No, it prefers B_4 to B_3. Ok.

    $ sudo port install rabbitmq-server

 Left to it's own devices - and time, about forty-five minutes later it turned its attention from erlang to zlib.
Two hours later, it was done. OK. Somehow, that line at the top of their RabbitMQ Server [page](http://www.rabbitmq.com/server.html)
was a optimistic. It finished with a note to remember:

    ###########################################################
    # A startup item has been generated that will aid in
    # starting rabbitmq-server with launchd. It is disabled
    # by default. Execute the following command to start it,
    # and to cause it to launch at startup:
    #
    # sudo launchctl load -w /Library/LaunchDaemons/org.macports.rabbitmq-server.plist
    ###########################################################

 It says that it should suffice to

    $ sudo rabbitmq-server

 It started. Cool.

### Experiments

  How does it behave for a simple [examples](../examples/examples.lisp)?

    ? (in-package :amqp-user)
    #<Package "DE.SETF.AMQP.USER">
    ? (setq *log-level* :debug)
    :DEBUG
    ? (defparameter *c* (make-instance 'amqp:connection :uri "amqp://guest:guest@localhost/"))
    [20100215T221437Z00] DEBUG #<CONNECTION #x50DB176>: open-connection: requesting version: #(65 77 81 80 1 1 0 9)/:AMQP-1-1-0-9-1.
    [20100215T221437Z00] DEBUG #<CONNECTION #x50DB176>: open-connection: byte-zero: 1, updated class to AMQP-1-1-0-9-1:CONNECTION.
    [20100215T221437Z00] DEBUG #<CHANNEL [amqp://localhost/].0 #x50DFC8E>: process-command: #<CONNECTION #x50DB176> #<CONNECTION.START #x50E1F66> . (:VERSION-MAJOR 8 :VERSION-MINOR 0 :SERVER-PROPERTIES (:|product| "RabbitMQ" :|version| "1.7.1" :|platform| "Erlang/OTP" :|copyright| "Copyright (C) 2007-2009 LShift Ltd., Cohesive Financial Technologies LLC., and Rabbit Technologies Ltd." :|information| "Licensed under the MPL.  See http://www.rabbitmq.com/") :MECHANISMS "PLAIN AMQPLAIN" :LOCALES "en_US")
    [20100215T221437Z00] DEBUG #<CONNECTION #x50DB176>: send-method: START-OK . (:CLIENT-PROPERTIES NIL :MECHANISM "PLAIN" :RESPONSE " guest guest" :LOCALE "en_US")
    [20100215T221437Z00] DEBUG #<CONNECTION #x50DB176>: encoding: CONNECTION.START-OK . (:CLIENT-PROPERTIES NIL :MECHANISM "PLAIN" :RESPONSE " guest guest" :LOCALE "en_US")
    [20100215T221437Z00] DEBUG #<CONNECTION #x50DB176>: send-method: #<CONNECTION.START-OK #x50E24C6>  #<OUTPUT-FRAME [(+)METHOD|0|c.0|t.0].{CONNECTION.START-OK}[36/4088: 0 10 0 11 0 0 0 0 ... 115 116 5 101 110 95 85 83] #x50E25BE>
    [20100215T221437Z00] DEBUG #<CHANNEL [amqp://localhost/].0 #x50DFC8E>: process-command: #<CONNECTION #x50DB176> #<CONNECTION.TUNE #x50E37F6> . (:CHANNEL-MAX 0 :FRAME-MAX 131072 :HEARTBEAT 0)
    [20100215T221437Z00] DEBUG #<CONNECTION #x50DB176>: send-method: TUNE-OK . (:CHANNEL-MAX 0 :FRAME-MAX 131072 :HEARTBEAT 0)
    [20100215T221437Z00] DEBUG #<CONNECTION #x50DB176>: encoding: CONNECTION.TUNE-OK . (:CHANNEL-MAX 0 :FRAME-MAX 131072 :HEARTBEAT 0)
    [20100215T221437Z00] DEBUG #<CONNECTION #x50DB176>: send-method: #<CONNECTION.TUNE-OK #x50E39FE>  #<OUTPUT-FRAME [(+)METHOD|0|c.0|t.0].{CONNECTION.TUNE-OK}[12/4088: 0 10 0 31 0 0 0 2 0 0 0 0] #x50E25BE>
    [20100215T221437Z00] DEBUG #<CONNECTION #x50DB176>: send-method: OPEN . (:VIRTUAL-HOST "/")
    [20100215T221437Z00] DEBUG #<CONNECTION #x50DB176>: encoding: CONNECTION.OPEN . (:VIRTUAL-HOST "/")
    [20100215T221437Z00] DEBUG #<CONNECTION #x50DB176>: send-method: #<CONNECTION.OPEN #x50E3D66>  #<OUTPUT-FRAME [(+)METHOD|0|c.0|t.0].{CONNECTION.OPEN}[8/4088: 0 10 0 40 1 47 0 0] #x50E25BE>
    [20100215T221437Z00] DEBUG #<CHANNEL [amqp://localhost/].0 #x50DFC8E>: process-command: #<CONNECTION #x50DB176> #<CONNECTION.OPEN-OK #x50E3FD6> . NIL
    [20100215T221437Z00] DEBUG #<CHANNEL [amqp://localhost/].1 #x50E4796>: send-method: OPEN . NIL
    [20100215T221437Z00] DEBUG #<CHANNEL [amqp://localhost/].1 #x50E4796>: encoding: CHANNEL.OPEN . ()
    [20100215T221437Z00] DEBUG #<CHANNEL [amqp://localhost/].1 #x50E4796>: send-method: #<CHANNEL.OPEN #x50E4ACE>  #<OUTPUT-FRAME [(+)METHOD|0|c.1|t.0].{CHANNEL.OPEN}[5/4088: 0 20 0 10 0] #x50E4BC6>
    [20100215T221437Z00] DEBUG #<CHANNEL [amqp://localhost/].1 #x50E4796>: process-command: #<CHANNEL [amqp://localhost/].1 #x50E4796> #<CHANNEL.OPEN-OK #x50E6D7E> . NIL
    [20100215T221437Z00] DEBUG #<CHANNEL [amqp://localhost/].1 #x50E4796>: Opened: NIL
    [20100215T221437Z00] DEBUG #<EXCHANGE #x50E74B6>: send-method: DECLARE . NIL
    [20100215T221437Z00] DEBUG #<EXCHANGE #x50E74B6>: encoding: EXCHANGE.DECLARE . (:EXCHANGE "ex" :TYPE "direct" :PASSIVE NIL :DURABLE NIL :NO-WAIT NIL :ARGUMENTS NIL)
    [20100215T221437Z00] DEBUG #<EXCHANGE #x50E74B6>: send-method: #<EXCHANGE.DECLARE #x50E799E>  #<OUTPUT-FRAME [(+)METHOD|0|c.1|t.0].{EXCHANGE.DECLARE}[21/4088: 0 40 0 10 0 0 2 101 ... 101 99 116 0 0 0 0 0] #x50E4BC6>
    [20100215T221437Z00] DEBUG #<CHANNEL [amqp://localhost/].1 #x50E4796>: process-command: #<EXCHANGE #x50E7CAE> #<EXCHANGE.DECLARE-OK #x50E7CEE> . NIL
    [20100215T221437Z00] DEBUG #<QUEUE #x50E7766>: send-method: DECLARE . NIL
    [20100215T221437Z00] DEBUG #<QUEUE #x50E7766>: encoding: QUEUE.DECLARE . (:QUEUE "q1" :PASSIVE NIL :DURABLE NIL :EXCLUSIVE NIL :AUTO-DELETE NIL :NO-WAIT NIL :ARGUMENTS NIL)
    [20100215T221437Z00] DEBUG #<QUEUE #x50E7766>: send-method: #<QUEUE.DECLARE #x50E7EE6>  #<OUTPUT-FRAME [(+)METHOD|0|c.1|t.0].{QUEUE.DECLARE}[14/4088: 0 50 0 10 0 0 2 113 49 0 0 0 0 0] #x50E4BC6>
    [20100215T221437Z00] DEBUG #<CHANNEL [amqp://localhost/].1 #x50E4796>: process-command: #<QUEUE #x50E8206> #<QUEUE.DECLARE-OK #x50E824E> . (:QUEUE "q1" :MESSAGE-COUNT 0 :CONSUMER-COUNT 0)
    [20100215T221437Z00] DEBUG q1: queue declared: q1 0 0
    [20100215T221437Z00] DEBUG #<QUEUE #x50E7766>: send-method: BIND . (:EXCHANGE "ex" :QUEUE "q1" :EXCHANGE #<AMQP-1-1-0-9-1:EXCHANGE #x50E74B6> :QUEUE #<AMQP-1-1-0-9-1:QUEUE #x50E7766> :ROUTING-KEY "/")
    [20100215T221437Z00] DEBUG #<QUEUE #x50E7766>: encoding: QUEUE.BIND . (:QUEUE "q1" :EXCHANGE "ex" :ROUTING-KEY "/" :NO-WAIT NIL :ARGUMENTS NIL)
    [20100215T221437Z00] DEBUG #<QUEUE #x50E7766>: send-method: #<QUEUE.BIND #x50E85AE>  #<OUTPUT-FRAME [(+)METHOD|0|c.1|t.0].{QUEUE.BIND}[19/4088: 0 50 0 20 0 0 2 113 ... 120 1 47 0 0 0 0 0] #x50E4BC6>
    [20100215T221437Z00] DEBUG #<CHANNEL [amqp://localhost/].1 #x50E4796>: process-command: #<QUEUE #x50E8206> #<QUEUE.BIND-OK #x50E889E> . NIL
    [20100215T221437Z00] DEBUG #<QUEUE #x50E8206>: bound.
    [20100215T221437Z00] DEBUG #<CHANNEL [amqp://localhost/].2 #x50E9006>: send-method: OPEN . NIL
    [20100215T221437Z00] DEBUG #<CHANNEL [amqp://localhost/].2 #x50E9006>: encoding: CHANNEL.OPEN . ()
    [20100215T221437Z00] DEBUG #<CHANNEL [amqp://localhost/].2 #x50E9006>: send-method: #<CHANNEL.OPEN #x50E933E>  #<OUTPUT-FRAME [(+)METHOD|0|c.2|t.0].{CHANNEL.OPEN}[5/4088: 0 20 0 10 0] #x50E9436>
    [20100215T221437Z00] DEBUG #<CHANNEL [amqp://localhost/].2 #x50E9006>: process-command: #<CHANNEL [amqp://localhost/].2 #x50E9006> #<CHANNEL.OPEN-OK #x50EB5EE> . NIL
    [20100215T221437Z00] DEBUG #<CHANNEL [amqp://localhost/].2 #x50E9006>: Opened: NIL
    [20100215T221437Z00] DEBUG #<BASIC #x50E7206>: send-method: PUBLISH . (:EXCHANGE "ex" :EXCHANGE #<AMQP-1-1-0-9-1:EXCHANGE #x50E74B6> :ROUTING-KEY "/")
    [20100215T221437Z00] DEBUG #<BASIC #x50E7206>: encoding: BASIC.PUBLISH . (:EXCHANGE "ex" :ROUTING-KEY "/" :MANDATORY NIL :IMMEDIATE NIL)
    [20100215T221437Z00] DEBUG #<BASIC #x50E7206>: send-method: #<BASIC.PUBLISH #x50EC126>  #<OUTPUT-FRAME [(+)METHOD|0|c.1|t.0].{BASIC.PUBLISH}[12/4088: 0 60 0 40 0 0 2 101 120 1 47 0] #x50E9436>
    [20100215T221437Z00] DEBUG #<BASIC #x50E7206>: encoding: (:CONTENT-TYPE "TEXT/PLAIN" :CONTENT-ENCODING "ISO-8859-1" :HEADERS (:ELEMENT-TYPE "STRING" :PACKAGE "DE.SETF.AMQP.USER") :DELIVERY-MODE 0 :PRIORITY 0 :CORRELATION-ID "" :REPLY-TO "" :EXPIRATION "" :MESSAGE-ID "" :TIMESTAMP 0 :TYPE "" :USER-ID "" :APP-ID "")
    [20100215T221437Z00] DEBUG #<CHANNEL [amqp://localhost/].1 #x50E4796>: Ended padding.
    [20100215T221437Z00] DEBUG #<BASIC #x50EBA76>: send-method: GET . (:QUEUE "q1" :QUEUE #<AMQP-1-1-0-9-1:QUEUE #x50EBD26>)
    [20100215T221437Z00] DEBUG #<BASIC #x50EBA76>: encoding: BASIC.GET . (:QUEUE "q1" :NO-ACK NIL)
    [20100215T221437Z00] DEBUG #<BASIC #x50EBA76>: send-method: #<BASIC.GET #x50EC8FE>  #<OUTPUT-FRAME [(+)METHOD|0|c.2|t.0].{BASIC.GET}[10/4088: 0 60 0 70 0 0 2 113 49 0] #x50E9436>
    > Error: Unexpected end of file on #<OPENTRANSPORT-BINARY-TCP-STREAM :INREL ->127.0.0.1:5672 #x50DC456>
    > While executing: DE.SETF.AMQP.IMPLEMENTATION::READ-7-BYTE-HEADER-FRAME
    > Type Command-. to abort.
    See the RestartsÉ menu item for further choices.
    1 >

We observe,  although RabbitMQ professes to be a 0.8r0 server, it accepts a 0.9r0 connection. Mysterious.
If we request, instead, 0.8r0 we observe

    ? (defparameter *c* (make-instance 'amqp:connection :uri "amqp://guest:guest@localhost/"))
    [20100216T180229Z00] DEBUG #<CONNECTION #x102F73F6>: open-connection: requesting version: #(65 77 81 80 0 0 9 1)/:AMQP-1-1-0-9-1.
    [20100216T180229Z00] DEBUG #<CONNECTION #x102F73F6>: open-connection: parsed version: #(65 77 81 80 1 1 8 0) / :AMQP-1-1-0-8-0.
    [20100216T180229Z00] DEBUG #<CONNECTION #x102F73F6>: open-connection: updated class to AMQP-1-1-0-8-0:CONNECTION.
    [20100216T180229Z00] DEBUG #<CONNECTION #x102F73F6>: open-connection: requesting version: #(65 77 81 80 1 1 8 0)/:AMQP-1-1-0-8-0.
    [20100216T180229Z00] DEBUG #<CONNECTION #x102F73F6>: open-connection: byte-zero: 1, connection class AMQP-1-1-0-8-0:CONNECTION.
    > Error: Frame length exceeds buffer size: 73728, #<VECTOR 4087 type (UNSIGNED-BYTE 8), simple>
    > While executing: CCL::%ASSERTION-FAILURE
    > Type Command-/ to continue, Command-. to abort.
    > If continued: test the assertion again.
    See the RestartsÉ menu item for further choices.

That's not good. What was the actual frame header?

    1 > (trace amqp.i::buffer-unsigned-byte-32)
    NIL
    1 > 
    Aborted
    ? (defparameter *c* (make-instance 'amqp:connection :uri "amqp://guest:guest@localhost/"))
    [20100216T191737Z00] DEBUG #<CONNECTION #x1030FF06>: open-connection: requesting version: #(65 77 81 80 0 0 9 1)/:AMQP-1-1-0-9-1.
    [20100216T191738Z00] DEBUG #<CONNECTION #x1030FF06>: open-connection: parsed version: #(65 77 81 80 1 1 8 0) / :AMQP-1-1-0-8-0.
    [20100216T191738Z00] DEBUG #<CONNECTION #x1030FF06>: open-connection: updated class to AMQP-1-1-0-8-0:CONNECTION.
    [20100216T191738Z00] DEBUG #<CONNECTION #x1030FF06>: open-connection: requesting version: #(65 77 81 80 1 1 8 0)/:AMQP-1-1-0-8-0.
    [20100216T191738Z00] DEBUG #<CONNECTION #x1030FF06>: open-connection: byte-zero: 1, connection class AMQP-1-1-0-8-0:CONNECTION.
     Calling (DE.SETF.AMQP.IMPLEMENTATION::BUFFER-UNSIGNED-BYTE-32 #(1 0 0 0 0 1 32 0) 4) 
     DE.SETF.AMQP.IMPLEMENTATION::BUFFER-UNSIGNED-BYTE-32 returned 2 values :
          73728
          8
    > Error: Frame length exceeds buffer size: 73728, #<VECTOR 4087 type (UNSIGNED-BYTE 8), simple>
    > While executing: CCL::%ASSERTION-FAILURE
    > Type Command-/ to continue, Command-. to abort.
    > If continued: test the assertion again.
    See the RestartsÉ menu item for further choices.
    1 > 

No, that's not good. We have a purportedly 0.8r0 broker which appears to use 7-byte frame headers.
Not quite what the [specification](http://www.amqp.org/confluence/download/attachments/720900/amqp0-8.pdf?version=1&modificationDate=1183135458000)(p.53) might suggest.
Turns [out](http://lists.rabbitmq.com/pipermail/rabbitmq-discuss/2010-February/006350.html) to have been a little misjudgement.

Whatever. At the moment, the frame type is bound to the protocol version,

    (defmethod make-input-frame ((connection amqp-1-1-0-8-0:connection) &rest args)
      (declare (dynamic-extent args))
      (apply #'make-instance 'amqp-1-1-0-8-0::input-frame
             :connection connection
             args))

    (defmethod make-output-frame ((connection amqp-1-1-0-8-0:connection) &rest args)
      (declare (dynamic-extent args))
      (apply #'make-instance 'amqp-1-1-0-8-0::output-frame
             :connection connection
             args))

which is not appropriate, as it may be necessary to stipulate the framing per broker implementation, rather than per protocol version.
Add the slots to the abstract amqp:connection class, shift the frame class binding, promote the frame classes, eg. `amqp-1-1-0-8-0::input-frame`
to `7-byte-header-input-frame`, etc and clean up, and try again.

    ? (defparameter *c* (make-instance 'amqp:connection :uri "amqp://guest:guest@localhost/")) 
    [20100217T005734Z00] DEBUG #<CONNECTION #x1D7394DE>: open-connection: requesting version: #(65 77 81 80 1 1 8 0)/:AMQP-1-1-0-8-0.
    [20100217T005734Z00] DEBUG #<CONNECTION #x1D7394DE>: open-connection: byte-zero: 1, connection class AMQP:CONNECTION.
    [20100217T005734Z00] DEBUG #<CONNECTION #x1D7394DE>: open-connection: updated class to AMQP-1-1-0-8-0:CONNECTION.
    [20100217T005734Z00] DEBUG #<CHANNEL [amqp://localhost/].0 #x1D73E086>: process-command: #<CONNECTION #x1D7394DE> #<CONNECTION.START #x1D740346> . (:VERSION-MAJOR 8 :VERSION-MINOR 0 :SERVER-PROPERTIES (:|product| "RabbitMQ" :|version| "1.7.1" :|platform| "Erlang/OTP" :|copyright| "Copyright (C) 2007-2009 LShift Ltd., Cohesive Financial Technologies LLC., and Rabbit Technologies Ltd." :|information| "Licensed under the MPL.  See http://www.rabbitmq.com/") :MECHANISMS "PLAIN AMQPLAIN" :LOCALES "en_US")
    [20100217T005734Z00] DEBUG #<CONNECTION #x1D7394DE>: send-method: START-OK . (:CLIENT-PROPERTIES NIL :MECHANISM "PLAIN" :RESPONSE " guest guest" :LOCALE "en_US")
    [20100217T005734Z00] DEBUG #<CONNECTION #x1D7394DE>: encoding: CONNECTION.START-OK . (:CLIENT-PROPERTIES NIL :MECHANISM "PLAIN" :RESPONSE " guest guest" :LOCALE "en_US")
    [20100217T005734Z00] DEBUG #<CONNECTION #x1D7394DE>: send-method: #<CONNECTION.START-OK #x1D7408A6>  #<7-BYTE-HEADER-OUTPUT-FRAME [(+)METHOD|0|c.0|t.0].{CONNECTION.START-OK}[36/4088: 0 10 0 11 0 0 0 0 ... 115 116 5 101 110 95 85 83] #x1D74099E>
    [20100217T005734Z00] DEBUG #<CHANNEL [amqp://localhost/].0 #x1D73E086>: process-command: #<CONNECTION #x1D7394DE> #<CONNECTION.TUNE #x1D741BD6> . (:CHANNEL-MAX 0 :FRAME-MAX 131072 :HEARTBEAT 0)
    [20100217T005734Z00] DEBUG #<CONNECTION #x1D7394DE>: send-method: TUNE-OK . (:CHANNEL-MAX 0 :FRAME-MAX 131072 :HEARTBEAT 0)
    [20100217T005734Z00] DEBUG #<CONNECTION #x1D7394DE>: encoding: CONNECTION.TUNE-OK . (:CHANNEL-MAX 0 :FRAME-MAX 131072 :HEARTBEAT 0)
    [20100217T005734Z00] DEBUG #<CONNECTION #x1D7394DE>: send-method: #<CONNECTION.TUNE-OK #x1D741DDE>  #<7-BYTE-HEADER-OUTPUT-FRAME [(+)METHOD|0|c.0|t.0].{CONNECTION.TUNE-OK}[12/4088: 0 10 0 31 0 0 0 2 0 0 0 0] #x1D74099E>
    [20100217T005734Z00] DEBUG #<CONNECTION #x1D7394DE>: send-method: OPEN . (:VIRTUAL-HOST "/")
    [20100217T005734Z00] DEBUG #<CONNECTION #x1D7394DE>: encoding: CONNECTION.OPEN . (:VIRTUAL-HOST "/" :CAPABILITIES "" :INSIST NIL)
    [20100217T005734Z00] DEBUG #<CONNECTION #x1D7394DE>: send-method: #<CONNECTION.OPEN #x1D74214E>  #<7-BYTE-HEADER-OUTPUT-FRAME [(+)METHOD|0|c.0|t.0].{CONNECTION.OPEN}[8/4088: 0 10 0 40 1 47 0 0] #x1D74099E>
    [20100217T005734Z00] DEBUG #<CHANNEL [amqp://localhost/].0 #x1D73E086>: process-command: #<CONNECTION #x1D7394DE> #<CONNECTION.OPEN-OK #x1D7423BE> . (:KNOWN-HOSTS "yoda:5672")
    DE.SETF.AMQP.USER::*C*
    ? (defparameter *ch1* (amqp:channel *c* :uri (uri "amqp:///")))
    [20100217T005738Z00] DEBUG #<CHANNEL [amqp://localhost/].1 #x1D7432BE>: send-method: OPEN . NIL
    [20100217T005738Z00] DEBUG #<CHANNEL [amqp://localhost/].1 #x1D7432BE>: encoding: CHANNEL.OPEN . (:OUT-OF-BAND "")
    [20100217T005738Z00] DEBUG #<CHANNEL [amqp://localhost/].1 #x1D7432BE>: send-method: #<CHANNEL.OPEN #x1D7435DE>  #<7-BYTE-HEADER-OUTPUT-FRAME [(+)METHOD|0|c.1|t.0].{CHANNEL.OPEN}[5/4088: 0 20 0 10 0] #x1D7436D6>
    [20100217T005738Z00] DEBUG #<CHANNEL [amqp://localhost/].1 #x1D7432BE>: process-command: #<CHANNEL [amqp://localhost/].1 #x1D7432BE> #<CHANNEL.OPEN-OK #x1D74588E> . NIL
    [20100217T005738Z00] DEBUG #<CHANNEL [amqp://localhost/].1 #x1D7432BE>: Opened: NIL
    DE.SETF.AMQP.USER::*CH1*
    ? (defparameter *ch1.basic* (amqp:basic *ch1*))
    DE.SETF.AMQP.USER::*CH1.BASIC*
    ? (defparameter *ch1.ex* (amqp:exchange *ch1*  :exchange "ex" :type "direct"))
    DE.SETF.AMQP.USER::*CH1.EX*
    ? (defparameter *ch1.q*  (amqp:queue *ch1* :queue "q1"))
    DE.SETF.AMQP.USER::*CH1.Q*
    ? (amqp:request-declare *ch1.ex*)
    [20100217T005756Z00] DEBUG #<EXCHANGE #x1D749B7E>: send-method: DECLARE . NIL
    [20100217T005756Z00] DEBUG #<EXCHANGE #x1D749B7E>: encoding: EXCHANGE.DECLARE . (:TICKET 0 :EXCHANGE "ex" :TYPE "direct" :PASSIVE NIL :DURABLE NIL :AUTO-DELETE NIL :INTERNAL NIL :NO-WAIT NIL :ARGUMENTS NIL)
    [20100217T005756Z00] DEBUG #<EXCHANGE #x1D749B7E>: send-method: #<EXCHANGE.DECLARE #x1D74BE0E>  #<7-BYTE-HEADER-OUTPUT-FRAME [(+)METHOD|0|c.1|t.0].{EXCHANGE.DECLARE}[21/4088: 0 40 0 10 0 0 2 101 ... 101 99 116 0 0 0 0 0] #x1D7436D6>
    [20100217T005756Z00] DEBUG #<CHANNEL [amqp://localhost/].1 #x1D7432BE>: process-command: #<EXCHANGE #x1D74C52E> #<EXCHANGE.DECLARE-OK #x1D74C816> . NIL
    #<AMQP-1-1-0-8-0:EXCHANGE #x1D749B7E>
    ? (amqp:request-declare *ch1.q*)
    [20100217T005801Z00] DEBUG #<QUEUE #x1D74AB96>: send-method: DECLARE . NIL
    [20100217T005801Z00] DEBUG #<QUEUE #x1D74AB96>: encoding: QUEUE.DECLARE . (:TICKET 0 :QUEUE "q1" :PASSIVE NIL :DURABLE NIL :EXCLUSIVE NIL :AUTO-DELETE NIL :NO-WAIT NIL :ARGUMENTS NIL)
    [20100217T005801Z00] DEBUG #<QUEUE #x1D74AB96>: send-method: #<QUEUE.DECLARE #x1D74E2AE>  #<7-BYTE-HEADER-OUTPUT-FRAME [(+)METHOD|0|c.1|t.0].{QUEUE.DECLARE}[14/4088: 0 50 0 10 0 0 2 113 49 0 0 0 0 0] #x1D7436D6>
    [20100217T005801Z00] DEBUG #<CHANNEL [amqp://localhost/].1 #x1D7432BE>: process-command: #<QUEUE #x1D74EA86> #<QUEUE.DECLARE-OK #x1D74EF0E> . (:QUEUE "q1" :MESSAGE-COUNT 0 :CONSUMER-COUNT 0)
    [20100217T005801Z00] DEBUG q1: queue declared: q1 0 0
    #<AMQP-1-1-0-8-0:QUEUE #x1D74AB96>
    ? (amqp:request-bind *ch1.q* :exchange *ch1.ex* :queue *ch1.q* :routing-key "/")
    [20100217T005811Z00] DEBUG #<QUEUE #x1D74AB96>: send-method: BIND . (:EXCHANGE "ex" :QUEUE "q1" :EXCHANGE #<AMQP-1-1-0-8-0:EXCHANGE #x1D749B7E> :QUEUE #<AMQP-1-1-0-8-0:QUEUE #x1D74AB96> :ROUTING-KEY "/")
    [20100217T005811Z00] DEBUG #<QUEUE #x1D74AB96>: encoding: QUEUE.BIND . (:TICKET 0 :QUEUE "q1" :EXCHANGE "ex" :ROUTING-KEY "/" :NO-WAIT NIL :ARGUMENTS NIL)
    [20100217T005811Z00] DEBUG #<QUEUE #x1D74AB96>: send-method: #<QUEUE.BIND #x1D75016E>  #<7-BYTE-HEADER-OUTPUT-FRAME [(+)METHOD|0|c.1|t.0].{QUEUE.BIND}[19/4088: 0 50 0 20 0 0 2 113 ... 120 1 47 0 0 0 0 0] #x1D7436D6>
    [20100217T005811Z00] DEBUG #<CHANNEL [amqp://localhost/].1 #x1D7432BE>: process-command: #<QUEUE #x1D74EA86> #<QUEUE.BIND-OK #x1D7505E6> . NIL
    [20100217T005811Z00] DEBUG #<QUEUE #x1D74EA86>: bound.
    #<AMQP-1-1-0-8-0:QUEUE #x1D74AB96>
    ? (defparameter *ch2* (amqp:channel *c* :uri (uri "amqp:///")))
    [20100217T005817Z00] DEBUG #<CHANNEL [amqp://localhost/].2 #x1D7513BE>: send-method: OPEN . NIL
    [20100217T005818Z00] DEBUG #<CHANNEL [amqp://localhost/].2 #x1D7513BE>: encoding: CHANNEL.OPEN . (:OUT-OF-BAND "")
    [20100217T005818Z00] DEBUG #<CHANNEL [amqp://localhost/].2 #x1D7513BE>: send-method: #<CHANNEL.OPEN #x1D7516DE>  #<7-BYTE-HEADER-OUTPUT-FRAME [(+)METHOD|0|c.2|t.0].{CHANNEL.OPEN}[5/4088: 0 20 0 10 0] #x1D7517D6>
    [20100217T005818Z00] DEBUG #<CHANNEL [amqp://localhost/].2 #x1D7513BE>: process-command: #<CHANNEL [amqp://localhost/].2 #x1D7513BE> #<CHANNEL.OPEN-OK #x1D75398E> . NIL
    [20100217T005818Z00] DEBUG #<CHANNEL [amqp://localhost/].2 #x1D7513BE>: Opened: NIL
    DE.SETF.AMQP.USER::*CH2*
    ? (defparameter *ch2.basic* (amqp:basic *ch2*))
    DE.SETF.AMQP.USER::*CH2.BASIC*
    ? (defparameter *ch2.q*  (amqp:queue *ch2* :queue "q1"))
    DE.SETF.AMQP.USER::*CH2.Q*
    ? (amqp:request-publish *ch1.basic* :exchange *ch1.ex*
                       :body (format nil "this is ~a" (gensym "test #"))
                       :routing-key "/")
    [20100217T005828Z00] DEBUG #<BASIC #x1D748E16>: send-method: PUBLISH . (:EXCHANGE "ex" :EXCHANGE #<AMQP-1-1-0-8-0:EXCHANGE #x1D749B7E> :ROUTING-KEY "/")
    [20100217T005829Z00] DEBUG #<BASIC #x1D748E16>: encoding: BASIC.PUBLISH . (:TICKET 0 :EXCHANGE "ex" :ROUTING-KEY "/" :MANDATORY NIL :IMMEDIATE NIL)
    [20100217T005829Z00] DEBUG #<BASIC #x1D748E16>: send-method: #<BASIC.PUBLISH #x1D75658E>  #<7-BYTE-HEADER-OUTPUT-FRAME [(+)METHOD|0|c.1|t.0].{BASIC.PUBLISH}[12/4088: 0 60 0 40 0 0 2 101 120 1 47 0] #x1D7517D6>
    [20100217T005829Z00] DEBUG #<BASIC #x1D748E16>: encoding: (:CONTENT-TYPE "TEXT/PLAIN" :CONTENT-ENCODING "ISO-8859-1" :HEADERS (:ELEMENT-TYPE "STRING" :PACKAGE "DE.SETF.AMQP.USER") :DELIVERY-MODE 0 :PRIORITY 0 :CORRELATION-ID "" :REPLY-TO "" :EXPIRATION "" :MESSAGE-ID "" :TIMESTAMP 0 :TYPE "" :USER-ID "" :APP-ID "" :CLUSTER-ID "")
    [20100217T005829Z00] DEBUG #<CHANNEL [amqp://localhost/].1 #x1D7432BE>: Ended padding.
    "this is test #44748"
    ? (amqp:request-get *ch2.basic* :queue *ch2.q*)
    [20100217T005835Z00] DEBUG #<BASIC #x1D75418E>: send-method: GET . (:QUEUE "q1" :QUEUE #<AMQP-1-1-0-8-0:QUEUE #x1D7547B6>)
    [20100217T005835Z00] DEBUG #<BASIC #x1D75418E>: encoding: BASIC.GET . (:TICKET 0 :QUEUE "q1" :NO-ACK NIL)
    [20100217T005835Z00] DEBUG #<BASIC #x1D75418E>: send-method: #<BASIC.GET #x1D75AE9E>  #<7-BYTE-HEADER-OUTPUT-FRAME [(+)METHOD|0|c.2|t.0].{BASIC.GET}[10/4088: 0 60 0 70 0 0 2 113 49 0] #x1D7517D6>
    [20100217T005835Z00] DEBUG #<CHANNEL [amqp://localhost/].2 #x1D7513BE>: process-command: #<BASIC #x1D75418E> #<BASIC.GET-OK #x1D75B8BE> . (:DELIVERY-TAG 1 :REDELIVERED NIL :EXCHANGE "ex" :ROUTING-KEY "/" :MESSAGE-COUNT 0)
    [20100217T005835Z00] DEBUG #<BASIC #x1D75418E>: respond-to-get, get-ok: (:DELIVERY-TAG 1 :REDELIVERED NIL :EXCHANGE "ex" :ROUTING-KEY "/" :MESSAGE-COUNT 0)
    [20100217T005835Z00] DEBUG #<CHANNEL [amqp://localhost/].2 #x1D7513BE>: process-command: #<BASIC #x1D75418E> #<HEADER #x3ED3B16> . (:FRAME #<7-BYTE-HEADER-INPUT-FRAME [(+)HEADER|0|c.2|t.0].[112/4088: 0 60 0 0 0 0 0 0 ... 0 0 0 0 0 0 0 0] #x1D75C54E> :CONTENT-TYPE "TEXT/PLAIN" :CONTENT-ENCODING "ISO-8859-1" :HEADERS (:ELEMENT-TYPE "STRING" :PACKAGE "DE.SETF.AMQP.USER") :DELIVERY-MODE 0 :PRIORITY 0 :CORRELATION-ID "" :REPLY-TO "" :EXPIRATION "" :MESSAGE-ID "" :TIMESTAMP 0 :TYPE "" :USER-ID "" :APP-ID "" :CLUSTER-ID "" :CLASS AMQP-1-1-0-8-0:BASIC :WEIGHT 0 :BODY-SIZE 19)
    [20100217T005835Z00] DEBUG #<CHANNEL [amqp://localhost/].2 #x1D7513BE>: device-read-content: in (STRING #<MIME:TEXT/PLAIN #x3D43A36>) in state #<AMQP.S:USE-CHANNEL.BODY.INPUT #x196B200E> x19
    [20100217T005835Z00] DEBUG #<BASIC #x1D75418E>: send-method: ACK . (:DELIVERY-TAG 1)
    [20100217T005835Z00] DEBUG #<BASIC #x1D75418E>: encoding: BASIC.ACK . (:DELIVERY-TAG 1 :MULTIPLE NIL)
    [20100217T005835Z00] DEBUG #<BASIC #x1D75418E>: send-method: #<BASIC.ACK #x1D75E8D6>  #<7-BYTE-HEADER-OUTPUT-FRAME [(+)METHOD|0|c.2|t.0].{BASIC.ACK}[13/4088: 0 60 0 80 0 0 0 0 0 0 0 1 0] #x1D7517D6>
    "this is test #44748"
    ? 

That's nice.
