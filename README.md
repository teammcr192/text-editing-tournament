# PROTOCOL (server prespective):
* Listen on port 9999 (temporary)
* Receive request message (connection type)
* if rejected: reply "CONNECTION_REJECTED"; end session
* create appropriate object of connection type

PROTOCOL (participant perspective):
    - Create a TCP connection to server
    - send "TEXT_EDITOR_TOURNAMENT_TYPE_PARTICIPANT"
    - await feedback:
        if not "CONNECTION_ACCEPTED", end session
    - send <user name>
    - send <user editor>
    - main "loop":
        -----
         top
        -----
        receive "CHALLENGE_INITIATE"
        send "CHALLENGE_ACCEPTED"
            - challenge number (id)
            - challenge title
            - "START_TEXT_TRANSMISSION"
            - challanege description (long text)
            - "END_TEXT_TRANSMISSION"
              -----
               standby (wait for server to start the challenge)
              -----
            - receive "FILE_TRANSFER_START", do:
                - receive file name w/ extension and directory
                - receive "START_TEXT_TRANSMISSION"
                - receive file contents
                - receive "END_TEXT_TRANSMISSION"
                    (make said file in said directory)
            - while not receive "FILE_TRANSFER_END"
              -----
               standby (wait for server to start the challenge)
              -----
            - receive "CHALLENGE_START"
              -----
       (1) ->  standby (wait for user to complete challenge)
               receive user submit message (from standard input)
              -----
            - send "CHALLENGE_SUBMISSION"
            - send challenge number (id)
            - send "FILE_TRANSFER_START", do:
                - send file name w/ extension and directory
                - send "START_TEXT_TRANSMISSION"
                - send file contents
                - send "END_TEXT_TRANSMISSION"
            - while there are files to send
            - send "FILE_TRANSFER_END"
            - if receive "CHALLENGE_RESULTS_INCORRECT":
                - receive "START_TEXT_TRANSMISSION"
                - receive diff report
                - receive "END_TEXT_TRANSMISSION"
                *GOTO (1)
            - else receive "CHALLENGE_RESULTS_CORRECT"
              -----
               standby (wait for challenge to finish)
              -----
            - receive "CHALLENGE_COMPLETE"
            - receive overall rank (#x of n competitors)
            - receive time it took you to complete
            - receive average completion time
            - recieve editor rank (#x of n on team txt_editor_here)
            - receive average completion time for all editors
            - receive "BEST_EDITOR_TIME_START", do:
                - receive best time editor
                - receive best time value
                - receive best time user
            - while not receive "BEST_EDITOR_TIME_END"
            GOTO (top)
          -----
           return to top
          -----

PROTOCOL (manager perspective):
    - Create a TCP connection to server
    - send "TEXT_EDITOR_TOURNAMENT_TYPE_MANAGER"
    - await feedback:
        if not "CONNECTION_ACCEPTED", end session
      -----
(1) -> from user input wait for challenge selection
       > send 1 challenge.txt
      -----
    - send "CHALLENGE_SEND_BEGIN"
    - if receive "CHALLENGE_REJECTED":
        print("Server rejected challenge (another challenge is in progress)")
        GOTO (1)
    - else receive "CHALLENGE_OKAY" and do:
        - send challenge number (id)
        - send challenge title
        - send "START_TEXT_TRANSMISSION"
        - send challanege description (long text)
        - send "END_TEXT_TRANSMISSION"
        - send "FILE_TRANSFER_START"
        - while has file to send:
            - send file name w/ extension and directory
            - send "START_TEXT_TRANSMISSION"
            - send file contents
            - send "END_TEXT_TRANSMISSION"
        - send "FILE_TRANSFER_END"
    - send "CHALLENGE_SEND_END"
      -----
       from user input wait for start challenge command
       > start
      -----
    - send "CHALLENGE_START"
    - receive "CHALLENGE_FINISH"


Data sending:
    Data is sent as bytes as characters.

Send on request:
    Client:
        TEXT_EDITOR_TOURNAMENT_TYPE_PARTICIPANT
    Manager:
        TEXT_EDITOR_TOURNAMENT_TYPE_MANAGER


Connection Stuff:
    CONNECTION_ACCEPTED
    CONNECTION_REJECTED
    CONNECTION_CLOSED


CLIENT:

    participant
    editor
    
