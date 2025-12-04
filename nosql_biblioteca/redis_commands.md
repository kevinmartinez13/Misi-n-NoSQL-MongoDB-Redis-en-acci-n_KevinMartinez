# redis_commands.md - Biblioteca Digital

# Conexión:
# redis-cli -h 127.0.0.1 -p 6379

## 1) Sessions (Strings) - TTL
SET session:user:1001 "{\"username\":\"ana123\",\"current_reading\":\"978-0143127741\"}" EX 3600
GET session:user:1001

## 2) Inventory quick access (Hash)
HSET inventory:978-0143127741 copies 5 reserved 0
HGETALL inventory:978-0143127741
HINCRBY inventory:978-0143127741 reserved 1
HINCRBY inventory:978-0143127741 copies -1

## 3) Reservation queue (List)
LPUSH reservation_queue "{"user":"user:1001","isbn":"978-0143127741","requested_at":"2025-06-01T10:00:00Z"}"
BRPOP reservation_queue 0

## 4) Popularity ranking (Sorted Set)
ZADD book_popularity 10 "978-0143127741"
ZINCRBY book_popularity 1 "978-0143127741"
ZRANGE book_popularity 0 9 WITHSCORES

## 5) Users online (Set)
SADD online_users user:1001
SMEMBERS online_users

## 6) TTL example (temporary hold)
SET hold:user:1001:978-0143127741 "reserved" EX 900  # 15 minutos

## 7) Streams for events (optional)
XADD loan_events * user_id 1001 isbn 978-0143127741 status loaned
