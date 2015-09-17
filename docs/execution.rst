Call Execution
==============

Call execution is the process through which the scheduled calls are executed at
the desired block number.  After a call has been scheduled, it can be executed
by account which chooses to initiate the transaction.  In exchange for
executing the scheduled call, they are paid a small fee of approximately 1% of
the gas cost used for executing the transaction.

Executing a call
----------------

Use the ``doCall`` function to execute a scheduled call.

* **Soldity Function Signature:** ``doCall(bytes32 callKey)``
* **ABI Signature:** ``0xfcf36918``

When this function is called, the following things happen.

1. A few are done to be sure that all of the necessary pre-conditions pass.  If
   any fail, the function exits early without executing the scheduled call:

   * the scheduler has enough funds to pay for the execution.
   * the call has not already been executed.
   * the call has not been cancelled.
   * the current block number is within the range this call is allowed to be
     executed.
2. The necessary funds to pay for the call are put on hold.
3. The call is executed via either the **authorizedAddress** or
   **unauthorizedAddress** depending on whether the scheduler is an authorized
   caller.
4. The gas cost and fees are computed, deducted from the scheduler's account,
   and deposited in the caller's account.


Setting transaction gas and gas price
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

It is best to supply the maximum allowed gas when executing a scheduled call as
the payment amount for executing the call is porportional to the amount of gas
used.  If the transaction runs out of gas, no payment is issued.

The payment is also dependent on the gas price for the executing transaction.
The lower the gas price supplied, the higher the payment will be.  (though you
should make sure that the gas price is high enough that the transaction will
get picked up by miners).


Getting your payment
^^^^^^^^^^^^^^^^^^^^

Payment for executing a call is deposited in your Alarm service account and can
be withdrawn using the account management api.


Determining what scheduled calls are next
-----------------------------------------

There following functions on the Alarm service facilitate querying for the next
scheduled calls.  None of these functions filter their return values based on
things like whether the scheduled call has been cancelled, but merely serve to
allow querying for calls in order.


getNextBlockWithCall
^^^^^^^^^^^^^^^^^^^^

* **Soldity Function Signature:** ``getNextBlockWithCall(uint blockNumber) returns (uint)``
* **ABI Signature:** ``0xe19eb0dd``

Returns the next block number for the next scheduled call on or after the
provided ``blockNumber``.  If there are no scheduled calls on or after
``blockNumber`` it returns ``0``.

getNextCallKey
^^^^^^^^^^^^^^

* **Soldity Function Signature:** ``getNextCallKey(uint blockNumber) returns (bytes32)``
* **ABI Signature:** ``0xaa6704da``

Returns the call key for the next scheduled call on or after the provided
``blockNumber``.  If there are no scheduled calls on or after ``blockNumber``
it returns ``0x0``;

getNextCallSibling
^^^^^^^^^^^^^^^^^^

* **Soldity Function Signature:** ``getNextCallSibling(bytes32 callKey) returns (bytes32)``
* **ABI Signature:** ``0x22bc71f``

Returns the call key for the next scheduled call that occurs on the same block
as the call referenced by the provided ``callKey``.  Returns ``0x0`` if there
are no subsequent calls on the same block.

Tips for executing scheduled calls
----------------------------------

The following tips may be useful if you wish to execute calls.

Only look in the next 40 blocks
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Since calls cannot be scheduled less than 40 blocks in the future, you can
count on the call ordering remaining static for the next 40 blocks.

No cancellation in next 8 blocks
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Since calls cannot be cancelled less than 8 blocks in the future, you don't
need to check cancellation status during the 8 blocks prior to its target
block.

Check that it was not already called
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you are executing a call after the target block but before the grace period
has run out, it is good to check that it has not already been called.

Check that the scheduler can pay
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

It is good to check that the scheduler has sufficient funds to pay for the
call's potential gas cost plus fees.
