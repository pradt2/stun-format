# stun-format

![Github](https://img.shields.io/badge/github-pradt2/stun_format-8da0cb?style=flat-square&labelColor=555555&logo=github")
![Crates.io](https://img.shields.io/crates/v/stun-format.svg?style=flat-square&color=fc8d62&logo=rust)
![Codacy grade](https://img.shields.io/codacy/grade/21362268c801439ca5adfbf76911c896?style=flat-square)
![Crates.io](https://img.shields.io/crates/l/stun-format?style=flat-square)

No-std parser for **all** RFC STUN messages.

## Highlights
 - **All** RFCs supported.
 - Support for each RFC can be enabled/disabled using feature flags.
 - No-std.
 - 100% code coverage.

## Examples

### Read STUN message

```rust
const MSG: [u8; 28] = [
    0x00, 0x01,                         // type: Binding Request
    0x00, 0x08,                         // length: 8 (header does not count)
    0x21, 0x12, 0xA4, 0x42,             // magic cookie
    0x00, 0x00, 0x00, 0x00,             
    0x00, 0x00, 0x00, 0x00,             
    0x00, 0x00, 0x00, 0x01,             // transaction id (16 bytes total incl. magic cookie)
    0x00, 0x03,                         // type: ChangeRequest
    0x00, 0x04,                         // length: 4 (only value bytes count)
    0x00, 0x00, 0x00, 0x40 | 0x20,      // change both ip and port
];

let msg = Msg::from(MSG.as_slice());

let typ = msg.typ()?;                   // MsgType::BindingRequest
let cookie = msg.cookie()?;             // 0x2112A442
let transaction_id = msg.tid()?;        // 0x01
let attr = msg.attrs_iter().next()?;    // Attr::ChangeRequest { change_ip: true, change_port: true }
```

### Create STUN message

```rust
const MSG: [u8; 28] = [
    0x00, 0x01,                         // type: Binding Request
    0x00, 0x08,                         // length: 8 (header does not count)
    0x21, 0x12, 0xA4, 0x42,             // magic cookie
    0x00, 0x00, 0x00, 0x00,
    0x00, 0x00, 0x00, 0x00,
    0x00, 0x00, 0x00, 0x01,             // transaction id (16 bytes total incl. magic cookie)
    0x00, 0x03,                         // type: ChangeRequest
    0x00, 0x04,                         // length: 4 (only value bytes count)
    0x00, 0x00, 0x00, 0x40 | 0x20,      // change both ip and port
];

let mut buf = [0u8; MSG.len()];
let mut msg = MsgBuilder::from(buf.as_mut_slice());

msg.typ(MsgType::BindingRequest)?;
msg.tid(1)?;
msg.add_attr(Attr::ChangeRequest { change_ip: true, change_port: true })?;

assert_eq!(&MSG, msg.as_bytes());
```

## Contribution guidelines

Pull requests are welcome. Please make sure your contribution adheres to the [Principles](#Principles) section above.
