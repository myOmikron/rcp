# RCP - Random Checksum Protocol

RCP's main use case is providing a checksum to validate requests between HTTP Servers, 
therefore each server has a shared secret to which all the others have to have access to.

## How to implement
- Put all your data from your POST or GET Request in a dictionary 
- Sort the dictionary alphanumerical by its keys.
- Concat its values to the respective key and join them: `key1value1key2value2...`
  - Note: When using non str values:
    - bool is represented as "true" or "false"
- Append the shared secret of your target
- Append current utc timestamp (unix epoch - just seconds)
- Optional: Add a salt (this may be the method's endpoint): `saltkey1value1...`
- Hash with SHA512
- Represent the hash as hex string (lowercase)
- The endpoint you're trying to reach should have the position stated, where the checksum should be put. 
This may be as a key in a POST request, or via Authentication Header.


**Watch out**
As this protocol heavily depends on retrieving the correct time, 
it may be required to enable NTP on your servers.

## How to use the reference implementation

**Get checksum**

```python
from rc_protocol import get_checksum

SHARED_SECRET = "s3cr3t_p@ssw0rd"

my_dict = {
    "key1": "value1",
    "key2": "value2"
}

checksum = get_checksum(my_dict, SHARED_SECRET)
```

**Validate checksum**

```python
from rc_protocol import validate_checksum

SHARED_SECRET = "s3cr3t_p@ssw0rd"

my_dict = {
    "key1": "value1",
    "key2": "value2",
}
checksum = "d0690e3c924e18bad866e2867698be75f64bdc6e809b76ffedb5c5095c9fbe15d36636b2df1fc47d2a3f348aea272ffc2fed4dc8ee08e0d13631ef646e1648c4"

if validate_checksum(my_dict, checksum, SHARED_SECRET):
    do_random_things()
else:
    print("You shall not pass.")
```

## Implementations
- Python [rc-protocol](https://pypi.org/project/rc-protocol/)
- Go [gorcp](https://github.com/myOmikron/gorcp)
- Rust [rc_protocol](https://crates.io/crates/rc_protocol)

