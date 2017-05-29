---
title: 日志封装 
tags: [android,数据]
---

# log 打印封装
> log 打印对调至关重要，而android log打印只能打印字符串，而我们在调试中大部分都是java 对象。许多人使用toString 方法来打印，虽然这个方法可行，但是对象里又二级对象就不适用而且比较麻烦。接下来就介绍我封装的logA对象


##使用gson配合打印

- gson 不管是对象数据还是集合类可以很直观的打印json对象
- 长度换行，log打印有长度显示，如果超出长度则调用调用另一个打印

```

	public static void e(Object content) {
		show("ee", content);
	}

	public static void a(Object content) {
		show("aa", content);
	}

	public static void texta(String str){
		if (str == null) {
			Log.i("aa", "str==null");
			return;
		}
		Log.i("aa", str);
	}


	public static void i(Object content) {
		show("ii", content);
	}

	private static void show(String name, Object obj) {
		if (obj == null) {
			Log.i(name, "str==null");
			return;
		}
		String str = new Gson().toJson(obj);
		int index = 0;
		int maxLength = 3000;
		String sub;
		if (str.length() < maxLength) {
			Log.i(name, str);
		} else {
			int num = str.length() / maxLength + 1;
			for (int i = 0; i < num; i++) {
				if (num - 1 == i) {
					Log.i(name, str.substring(i * maxLength, str.length()));
				} else {
					Log.i(name,
							str.substring(i * maxLength, (i + 1) * maxLength));
				}
			}

		}
	}
```

