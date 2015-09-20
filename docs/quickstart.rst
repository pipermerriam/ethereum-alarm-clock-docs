Getting Started
===============

Here you will be guided through a simple example to demonstrate how to use the
Alarm service.


* Trust Fund Contract
* Deploy
* Monitor


.. code-block:: solidity

    contract AlarmAPI {
        function authorizedAddress() returns (address);
        function unauthorizedAddress() returns (address);
    }
    
    contract TrustFund {
        address trustee = 0x...;
        address _alarm = 0x...;

        function releaseFunds() public {
            AlarmAPI alarm = AlarmAPI(_alarm);
            if (msg.sender == alarm.authorizedAddress()) {
                suicide(trustee);
            }
        }
    }
