# Running Jobs on Nomad

With the Nomad servers and agents fully bootstrapped you now have the ability to submit and run [Jobs](https://www.nomadproject.io/docs/operating-a-job/index.html).

## Configure the Nomad Client

Before submitting Jobs the local Nomad client must be configure with the remote Nomad cluster details. This can be done by setting the following environment variables:

```
NOMAD_ADDR
NOMAD_CACERT
NOMAD_CLIENT_CERT
NOMAD_CLIENT_KEY
```

Source the `nomad.env` shell script to populate the necessary environment variables for the current shell session:

```
source nomad.env
```

## Run the Example Jobs

There are two example Jobs under the jobs directory:

* `ping` - runs the ping command against the google.com domain.
* `token-printer` - loads a Vault token from a file and prints it to stderr.

### Run the ping Job

The `ping` Job runs the ping command against the google.com domain 1000 times then exits.

Execute a plan for the `ping` Job:

```
nomad plan jobs/ping.nomad
```

Submit and run the `ping` Job:

```
nomad run jobs/ping.nomad
```

Check the status of the `ping` Job:

```
nomad status ping
```

Retrieve and view the logs for the `ping` Job:

```
nomad logs -job ping
```

The `ping` Job is set to run continuously. After every successful run Nomad will automatically restart the Job.

Stop and purge the `ping` Job:

```
nomad stop -purge ping
```

### Run the token printer Job

The `token-printer` Job demonstrates Nomad's native Vault integration. The `token-printer` Job specification requests a Vault token attached to the default Vault profile. The Nomad agent that accepts and runs the `token-printer` Job will populate a token in a file named `vault_token` under the running Job's secret directory. The `token-printer` job will continuously monitor the token file for changes while printing the current value to stderr.

Execute a plan for the `token-printer` Job:

```
nomad plan jobs/token-printer.nomad
```

Submit and run the `token-printer` Job:

```
nomad run jobs/token-printer.nomad
```

Retrieve and view the logs for the `token-printer` Job:

```
nomad logs -f -stderr -job token-printer
```

The `token-printer` Job is set to run continuously and will automatically reload the Vault token from disk after receiving a `NOHUP` single from the local Nomad agent.

Stop and purge the `token-printer` Job:

```
nomad stop -purge token-printer
```