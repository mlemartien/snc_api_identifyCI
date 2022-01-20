# API Code Examples: identifyCI

This code snippet is an example for the ServiceNow IRE API `SNC.IdentificationEngineScriptableApi.identifyCI`.

In this example we try to identify a hardware CI using a combination of its name (direct identification) and one of its network adapters (lookup identification). CMDB table `cmdb_ci_network_adapter` has a reference field `cmdb_ci` referencing its parent CI.

Example:
```js
// If CI has been identified, will end up in this GlideRecord
var grHardware;

// Build the payload to identify this CI 
var identificationPayload = {
    items: [
        {
            "className": "cmdb_ci_hardware",
            "values": {
                "name": "*JEMPLOYEE-IBM"
            },
            "lookup": [
                {
                    "className": "cmdb_ci_network_adapter",
                    "values": {
                        "name": "eth0",
                        "mac_address": "00:13:CE:C9:16:5D"
                    }
                },
                {
                    "className": "cmdb_ci_network_adapter",
                    "values": {
                        "name": "eth1",
                        "mac_address": "00:14:A4:DE:A6:E2"
                    }
                }
            ]
        }
    ]
};

// Using IRE, identify the CI
var output = JSON.parse(SNC.IdentificationEngineScriptableApi.identifyCI(JSON.stringify(identificationPayload)));

// Using the IRE operation output, determine whether the CI exist
// and if so fetch it
if (JSUtil.notNil(output)) {
    if (output.items[0].operation !== "INSERT") {
        grHardware = new GlideRecordUtil().getCIGR(output.items[0].sysId);
    }
}

// Display the output
if (JSUtil.notNil(grHardware)) {
    gs.debug("Successfully identified the hardware " + grHardware.getDisplayValue() + " of class " + grHardware.getValue("sys_class_name"));
} else {
    gs.debug("This hardware does not exist");
}
```

Output:
```
*** Script: [DEBUG] Successfully identified the hardware *JEMPLOYEE-IBM of class cmdb_ci_computer
```
