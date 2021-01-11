# Safety concept
## Safety goal: while requested, the system shall display the driver warning within 200 ms or transition to the safe state within 200 ms.
* [Information] Information: “while ” means that, if the telltale request persists/is repeated, the system has to continue to display the telltale.
* [Information] The 200 ms include the time needed for the request to reach the Cluster demo. This is considered in the frequency of the cyclic communication.
* [FSR-001] [B] The Telltale requester shall send a request cyclically controlling whether a telltale is needed to be shown or not.
* [FSR-002] [B] The communication between telltale requester and cluster controller shall be E2E protected against data corruption and message loss.
* [FSR-003] [B] The Instrument cluster shall display the requested telltale or transition to the safe state
    * [Information] We implement this by splitting into a QM path rendering the Display and a Safety path checking whether the requested telltale is shown or not
    * [FSR-004] [QM] Th QT app shall render the image within 70ms of the cluster controller receiving the message
    * [FSR-005] [B] The Instrument cluster controller shall transition the system to the safe state, if the requested telltale is not shown.
        * [Information] Safety-signal source part of the control flow
        * [TSR-001] [B] All inputs to the safety-signal-source shall be E2E protected
        * [TSR-002] [B] On E2E miss of any input to Safety-signal-source, Safety-signal-source shall request "Safe state" from the safety-app
        * [TSR-003] [B] The safety-signal-source shall determine, whether the requested telltale is shown
        * [TSR-004] [B] If the requested telltale is not shown, the Safety-signal-source shall request "Safe state" from the safety app.
        * [TSR-005] [QM] The safety-signal source shall cyclically send the safety status message to the safety app
        * [TSR-006] [B] Communication between Safety signal source and Safety App shall be E2E protected
        * [TSR-007] [B] The results of the Safety signal source workload shall deterministically depend on the inputs
            * [Information] This implies freedom from spatial interference between the safety-signal-source / safety app and the rest of the (Operating) system, if taken at face value. The formulation is deliberate, the Architecture Workgroup is analysing all potential ways such interference could happen, we then revisit this requirement to refine it regarding safety mechanisms on the application level handling the determined interference scenarios, where possible to avoid putting undue burden on the kernel.
            * [Information] Hardware faults are out of scope, see assumptions
            * [Information] Temporal interference is not relevant here, since the watchdog transitions the system into the safe state, if execution takes too long.
        * [Information] Safety App portion of the Control Flow
        * [TSR-008] [B] The Safety App shall check the Communication from Safety Signal Source for E2E misses
        * [TSR-009] [B] The Safety App shall pet the external watchdog, if and only if the cyclic message from the safety signal source passes the E2E check and does not request "safe state"
        * [TSR-010] [B] The results of the Safety-app workload shall deterministically depend on the inputs
            * [Information] This implies freedom from spatial interference between the safety-signal-source / safety app and the rest of the (Operating) system, if taken at face value. The formulation is deliberate, the Architecture Workgroup is analysing all potential ways such interference could happen, we then revisit this requirement to refine it regarding safety mechanisms on the application level handling the determined interference scenarios, where possible to avoid putting undue burden on the kernel.
            * [Information] Hardware faults are out of scope, see assumptions
            * [Information] Temporal interference is not relevant here, since the watchdog transitions the system into the safe state, if execution takes too long.
        * [Information] Watchdog part of the control flow
        * [FSR-006] [B] The watchdog shall kill the backlight of the Cluster Display within 50ms, if it is not triggered within 150ms.
* [FSR-007] [B] The chain between Telltale request sent and display/safe state shall be less than 200ms.
    * [Information] Timing allocation considerations:
The timings for rendering and telltale verification are not safety relevant, since the watchdog transitions to the system to the safe state, if the chain takes too long.
        * [Information] Signal sending including rendering by QT app: 100ms. We assume the time delay between the requester sending the message, and the cluster demo receiving it is less than 30ms, leaving 70ms for the rendering
        * [Information] Display check inklusive WD trigger: 50ms
        * [Information] Watchdog logic inclusive backlight killing: 50ms
    * [FSR-008] [B] The Telltale request message shall be sent every 200 ms
    * [FSR-009] [QM] Th QT app shall render the image within 70ms of the cluster controller receiving the message
    * [FSR-010] [QM] Verification of telltale shown shall be performed within 50ms
    * [FSR-011] [B] The watchdog shall kill the backlight of the Cluster Display within 50ms, if it is not triggered within 150ms.
## Safety goal: The system shall transition to the safe state within 100ms of the display showing an unrequested telltale for longer than 100 ms
* [Information] We need to discuss this, this might not work with the frequency of 200ms we have in SZ1, it will if we relax it a little bit to around 120ms, see
* [FSR-012] [B] The Telltale requester shall send a request cyclically controlling whether a telltale is needed to be shown or not.
* [FSR-013] [B] The communication between telltale requester and cluster controller shall be E2E protected against data corruption and message loss.
* [FSR-014] [B] The instrument cluster shall transition to the safe state within 50ms, if it detects an unrequested telltale being displayed for more than 100 ms
    * [TSR-011] [B] All inputs to the safety-signal-source shall be E2E protected
    * [TSR-012] [B] On E2E miss of any input to Safety-signal-source, Safety-signal-source shall request "Safe state" from the safety-app
    * [TSR-013] [B] The safety-signal-source shall determine, whether a non requested telltale is displayed
    * [TSR-014] [B] If a non requested telltale is displayed for more than 100ms, the safety-signal source shalll request "Safe state" from the safety app.
    * [TSR-015] [QM] The safety-signal source shall cyclically send the safety status message to the safety app
    * [TSR-016] [B] Communication between Safety signal source and Safety App shall be E2E protected
    * [TSR-017] [B] The results of the Safety signal source workload shall deterministically depend on the inputs
        * [Information] This implies freedom from spatial interference between the safety-signal-source / safety app and the rest of the (Operating) system, if taken at face value. The formulation is deliberate, the Architecture Workgroup is analysing all potential ways such interference could happen, we then revisit this requirement to refine it regarding safety mechanisms on the application level handling the determined interference scenarios, where possible to avoid putting undue burden on the kernel.
        * [Information] Hardware faults are out of scope, see assumptions
        * [Information] Temporal interference is not relevant here, since the watchdog transitions the system into the safe state, if execution takes too long.
    * [Information] Safety App portion of the Control Flow
    * [TSR-018] [B] The Safety App shall check the Communication from Safety Signal Source for E2E misses
    * [TSR-019] [B] The Safety App shall pet the external watchdog, if and only if the cyclic message from the safety signal source passes the E2E check and does not request "safe state"
    * [TSR-20] [B] The results of the Safety-app workload shall deterministically depend on the inputs
        * [Information] This implies freedom from spatial interference between the safety-signal-source / safety app and the rest of the (Operating) system, if taken at face value. The formulation is deliberate, the Architecture Workgroup is analysing all potential ways such interference could happen, we then revisit this requirement to refine it regarding safety mechanisms on the application level handling the determined interference scenarios, where possible to avoid putting undue burden on the kernel.
        * [Information] Hardware faults are out of scope, see assumptions
        * [Information] Temporal interference is not relevant here, since the watchdog transitions the system into the safe state, if execution takes too long.
    * [Information] Watchdog part of the control flow
    * [FSR-015] [B] The watchdog shall kill the backlight of the Cluster Display within 50ms, if it is not triggered within 150ms.
* [FSR-016] [B] The chain between Telltale request sent and display/safe state shall be less than 200ms.
    * [Information] Timing allocation considerations:
The timings for rendering and telltale verification are not safety relevant, since the watchdog transitions to the system to the safe state, if the chain takes too long.
        * [Information] Signal sending including rendering by QT app: 100ms. We assume the time delay between the requester sending the message, and the cluster demo receiving it is less than 30ms, leaving 70ms for the rendering
        * [Information] Display check inklusive WD trigger: 50ms
        * [Information] Watchdog logic inclusive backlight killing: 50ms
    * [FSR-017] [B] The Telltale request message shall be sent every 200 ms
    * [FSR-018] [QM] Th QT app shall rendertig
    *  the image within 70ms of the cluster controller receiving the message
    * [FSR-019] [QM] Verification of telltale shown shall be performed within 50ms
    * [FSR-020] [B] The watchdog shall kill the backlight of the Cluster Display within 50ms, if it is not triggered within 150ms.
