# redis-lua

### Connect to a redis-server instance and sent a PING command

```lua
local redis = require 'redis'
local client = redis.connect('118.25.50.86',6379)
local response = client:ping()		--true
```

