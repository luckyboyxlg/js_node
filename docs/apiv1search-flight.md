/api/v1/search-flight



搜  encrypted



![image-20250226135745201](/Users/lucky/Library/Application Support/typora-user-images/image-20250226135745201.png)

```javascript
{
    "currency": "USD",
    "departureDate": "2025-03-02",
    "daysBeforeDeparture": 0,
    "daysAfterDeparture": 0,
    "departurePlace": "HAN",
    "arrival": "DAD",
    "oneway": 1,
    "adultCount": 1,
    "childCount": 0,
    "infantCount": 0,
    "requestId": "IP15U3N6XRNE-1740552815375",
    "sessionId": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjNMZTZEak1WQnhUZG5mT283Wlg3RGZRQ0ZwWm10QzFTeUtTSHpBbEM1c1daSGI5a1NlQzFYQ1JvRWZNZHd3cHYiLCJpc3MiOiJ2ai13ZWJzaXRlLW1vbml0b3JpbmciLCJpYXQiOjE3NDA1NTI4MDksImV4cCI6MTc0MDU1NDYwOX0.IZqhJEJrcRlhvUBllrCUgcWLFLXU7gIg9a4UHBHnFcL8iMjbgCq-N2d_kndgs2cxfjZkPr7uCXqjd31vnncKRpQZax7JkZUZmvlFKsMEDUr3zfN5i7Q3EhLD0hqPXEv_a1B_VWJ9sVDjkhOFs7TJhCYjdHA9IvfcUdYcepS6gib1mkn-rHFJToyLbYWGqZCoF6XA26yzPe-n4gqHa4H-8g_o3Ug-mi2U80ZnKdf_4woaJMLL0MkpfmwzDeyJGzVycG4sHExeUmag1NdAeImHIrU7GKaYdwd7uLOqPjUaUcvjS5pTNCpCZ2j182zaO6XPyS2LWvlf83eG3KV37Y8eIg",
    "x-power-web-s-d": "1119569115-1300511021-4c6b-b611-33878eda96ed",
    "user-agent-ls-data": "09da39f0-a6f8-4fe7-8b2c-a7594011f804-1736993978131",
    "_signature": "341a147187e2b734f89e9bea2790e51fdc21854d813a0faebd81ac8e721731d4"
}
```

**x-power-web-s-d**

**requestId**: "MMJL3AMAZ8B0-1740552098792"

将去掉_signature的上面的对象，进行按照key进行升序排序拼接成key=value&的格式，在进行sha256加密

**_signature****: "70207b7b58348d8cbea1c13851cb9d2729a04c895e885a07fdc21747b735b834"

每次不同

sessionId 从sessionStorage 里获取的，然后是通过响应里拿到的

```
// 保存原始的 sessionStorage 方法
const originalSetItem = sessionStorage.setItem;
const originalGetItem = sessionStorage.getItem;

// Hook setItem 方法
sessionStorage.setItem = function(key, value) {
    
    if(key=="sessionId"){
    	console.log(`Setting item with key: ${key}, value: ${value}`);
    	debugger;
    }
    
    // 在这里可以添加额外的逻辑，比如数据验证或修改
    
    // 调用原始方法
    return originalSetItem.apply(this, arguments);
};

// Hook getItem 方法
sessionStorage.getItem = function(key) {

    const value = originalGetItem.apply(this, arguments);
    //if(key=="sessionId"){
      //  console.log(`Getting item with key: ${key}`);
    	//console.log(value);
    	//debugger;
    //}
    // 在这里可以添加额外的逻辑，比如日志记录或者数据转换
    
    // 调用原始方法
   
    
    // 可以在这里处理获取到的数据
    return value;
};
```



![image-20250227094104290](./imgs/apiv1search-flight.assets/image-20250227094104290.png)

"FX8RX0IRVZ8W-1740559945632"

hook toUpperCase

jsonString后进行 Buffer, ArrayBuffer, Array 处理 1280长度

变成 Uint8Array(1028) 

buffer处理的代码

```
e.prototype.encrypt = function(e, t) {
                var r = []
                  , i = []
                  , o = e.length
                  , a = Math.ceil(o / this.maxMessageLength) || 1
                  , s = Math.ceil(o / a || 1);
                if (1 == a)
                    r.push(e);
                else
                    for (var u = 0; u < a; u++)
                        r.push(e.slice(u * s, (u + 1) * s));
                for (var c = 0; c < r.length; c++)
                    i.push(this.encryptEngine.encrypt(r[c], t));
                return n.concat(i)
            }
```

![image-20250226143134789](/Users/lucky/Library/Application Support/typora-user-images/image-20250226143134789.png)

```
t.fromByteArray = function(e) {
        for (var t, r = e.length, i = r % 3, o = [], a = 0, s = r - i; a < s; a += 16383)
            o.push(f(e, a, a + 16383 > s ? s : a + 16383));
        return 1 === i ? (t = e[r - 1],
        o.push(n[t >> 2] + n[t << 4 & 63] + "==")) : 2 === i && (t = (e[r - 2] << 8) + e[r - 1],
        o.push(n[t >> 10] + n[t >> 4 & 63] + n[t << 2 & 63] + "=")),
        o.join("")
    }
```





stringify里都被修改了





![image-20250304145822380](./imgs/apiv1search-flight.assets/image-20250304145822380.png)
