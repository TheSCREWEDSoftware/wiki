# mail

[<-Back-to:Characters](database-characters)

**The \`mail\` table**

This table contains main data about all mails in the game.

**Table Structure**

| Field                             | Type     | Attributes | Key | Null | Default | Extra | Comment                            |
| --------------------------------- | -------- | ---------- | --- | ---- | ------- | ----- | ---------------------------------- |
| [id](#id)                         | INT      | UNSIGNED   | PRI | NO   | 0       |       | Identifier                         |
| [messageType](#messagetype)       | TINYINT  | UNSIGNED   |     | NO   | 0       |       |                                    |
| [stationery](#stationery)         | TINYINT  | UNSIGNED   |     | NO   | 41      |       |                                    |
| [mailTemplateId](#mailtemplateid) | SMALLINT | UNSIGNED   |     | NO   | 0       |       |                                    |
| [sender](#sender)                 | INT      | UNSIGNED   |     | NO   | 0       |       | Character Global Unique Identifier |
| [receiver](#receiver)             | INT      | UNSIGNED   |     | NO   | 0       |       | Character Global Unique Identifier |
| [subject](#subject)               | LONGTEXT | SIGNED     |     | YES  |         |       |                                    |
| [body](#body)                     | LONGTEXT | SIGNED     |     | YES  |         |       |                                    |
| [has_items](#hasitems)            | TINYINT  | UNSIGNED   |     | NO   | 0       |       |                                    |
| [expire_time](#expiretime)        | INT      | UNSIGNED   |     | NO   | 0       |       |                                    |
| [deliver_time](#delivertime)      | INT      | UNSIGNED   |     | NO   | 0       |       |                                    |
| [money](#money)                   | INT      | UNSIGNED   |     | NO   | 0       |       |                                    |
| [cod](#cod)                       | INT      | UNSIGNED   |     | NO   | 0       |       |                                    |
| [checked](#checked)               | TINYINT  | UNSIGNED   |     | NO   | 0       |       |                                    |
| [auctionId](#checked)             | INT      | UNSIGNED   |     | NO   | 0       |       |                                    |

**Description of the fields**

### id

This field contains unique ID of all mails/messages.

### messageType

| ID  | Name            | Description                 |
| --- | --------------- | --------------------------- |
| 0   | MAIL_NORMAL     |
| 2   | MAIL_AUCTION    | Sent by Auction House       |
| 3   | MAIL_CREATURE   | Sent by a NPC               |
| 4   | MAIL_GAMEOBJECT | Sent by an Object           |
| 5   | MAIL_CALENDAR   | Calendar Event Notification |

Note: ID `1` doesn't exist.

### stationery

This field can contain these values and defines the background texture of the mail:

| ID  | Name                    | Description              |
| --- | ----------------------- | ------------------------ |
| 1   | MAIL_STATIONERY_TEST    | Test Background          |
| 41  | MAIL_STATIONERY_DEFAULT | Normal Background        |
| 61  | MAIL_STATIONERY_GM      | Blizzard / GM Background |
| 62  | MAIL_STATIONERY_AUCTION | Auction Background       |
| 64  | MAIL_STATIONERY_VAL     | Valentine's Background   |
| 65  | MAIL_STATIONERY_CHR     | Christmas's Background   |
| 67  | MAIL_STATIONERY_ORP     | Orphan's Background      |

### mailTemplateId

Id from MailTemplate.dbc

### sender

This refers to the Unique ID of the sender from the possible entries below:

- [auctionhouse.id](auctionhouse#id)
- [calendar_events.id](calendar_events#id)
- [character.guid](character#guid)
- [creature_template.entry](creature_template#entry)
- [gameobject_template.entry](gameobject_template#entry)

Note: ID `0` is used when a unknown sender case.

### receiver

This refers to [character.guid](character#guid) of the player receiving.

### subject

Here is stored mail's subject.

If [stationery](#stationery) value is `62` it will be stored like this:

Example: `14047:0:1:2:5`

This would be item `14047` (Runecloth) : `0` (always 0) : `1` (Auction won) : `2` (Auction House ID) : `5` (Quanity of 5 Runecloths)

Format: [item_template.entry](item_template#entry) : `0` : `<Auction Message Flag>` : [auctionhouse.id](auctionhouse#id) : `<Amount of the Item>`

#### Auction Message Flag

| Flag | Comment                     |
| ---- | --------------------------- |
| 0    | AUCTION_OUTBIDDED           |
| 1    | AUCTION_WON                 |
| 2    | AUCTION_SUCCESSFUL          |
| 3    | AUCTION_EXPIRED             |
| 4    | AUCTION_CANCELLED_TO_BIDDER |
| 5    | AUCTION_CANCELED            |
| 6    | AUCTION_SALE_PENDING        |


### body

The text contained in the mail. Max length is 8000 characters.

If [stationery][3] is 62, body has formatted data:

`hexID:bid:buyout:deposit:cut:delay:eta`

- **hexID**: hex value of itemowner's GUID (guid field from characters table)

- **bid**: ending bid for this lot

- **buyout**: buyout price of lot

- **deposit**: amount of money which will be taken by auctionhouse and returned then auction ends

- **cut**: Commission fee. Will be taken by auctionhouse then auction ends

- **delay**: time in seconds to delay mail with money for successfully solded lot

- **eta**: packed time to next mail whth money which appears in mail heder and body of notification mail

This formatted data seen only in mail with notification about successful auction or about pending mail with money.

### has_items

Default: 0,

When is set to 1, that mail can contain items.

For items look at [mail_items](mail_items) table.

### expire_time

Here is timestamp which stores date for auto-return mail to sender or delete if [stationery][3] is 62 (AuctionHouse).

### deliver_time

Here is timestamp which stores date when mail must be delivered to receiver. Can be delayed mails from AuctionHouse.

### money

The ammout of money in mail, or money to pay when is COD.

### cod

Default: 0 - No COD,

when is set to 1, that field \`money\` stores gold for COD.

### checked

| Flag | Comment                     |
| ---- | --------------------------- |
| 0    | MAIL_CHECK_MASK_NONE        |
| 1    | MAIL_CHECK_MASK_READ        |
| 2    | MAIL_CHECK_MASK_RETURNED    |
| 4    | MAIL_CHECK_MASK_COPIED      |
| 8    | MAIL_CHECK_MASK_COD_PAYMENT |
| 16   | MAIL_CHECK_MASK_HAS_BODY    |

### auctionId

Only if [stationery][3] is 62.

Lot id from AuctionHouse. Can be negative vector in case of delayed mail with money sended by Auction to Lot-owner.
For example:

[auctionId][14] = 777 : mail to Lot-owner, contains money for sended Lot id 777. Delivered money.

[auctionId][14] = -777 : mail contains info that Lot id 777 is sold. Money will be delivered in next mail, time of deliver is set in [`deliver_time`][11] field.
