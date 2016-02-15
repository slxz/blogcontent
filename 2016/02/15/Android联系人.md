title: Android联系人
date: 2016-02-15
tags: Android, ContentProvider, ContactsContract
---
Android联系人的主要结构可以看[这里的介绍](http://www.android-doc.com/guide/topics/providers/contacts-provider.html)。
主要分成三层结构，顶层就是代表一个人，第二层则代表这个人在不同帐号类型下的帐号，以域名作为帐号类型区分。比如谷歌帐号和Twitter帐号。第三层就是实际数据存储的部分。参见下图
![联系人表结构](http://www.android-doc.com/images/providers/contacts_structure.png)


对应到代码中，可以参见[`ContactsContract`类](http://www.android-doc.com/reference/android/provider/ContactsContract.html)
### ContactsContract
`ContactsContract`类中含有大量接口和内部类，`Contacts`,`RawContacts`和`Data`这三个就是对应了上图的三层结构。

但是如果直接从这里查的话，数据太过松散，比如要查某个电话号码，则十分麻烦，data中号码和其他的数据并没有明显的区分。

### CommonDataKinds
[`CommonDataKinds`](http://www.android-doc.com/reference/android/provider/ContactsContract.CommonDataKinds.html)就是来解决上面的问题，
这个类中也提供了各种内部类来区分具体信息，如`Nickname`,`Phone`,`Email`等类，这里就提供了具体信息，通过使用ContentProvider的逻辑来查找。

#### 示例
```java
Uri contactUri = ContactsContract.Contacts.CONTENT_URI;
ContentResolver cr = getActivity().getContentResolver();
Cursor cursor;
cursor = cr.query(contactUri, null, null, null, null);
while (cursor.moveToNext()) {
String id = cursor.getString(cursor.getColumnIndex(ContactsContract.Contacts._ID));
String name = cursor.getString(cursor.getColumnIndex(ContactsContract.Contacts.DISPLAY_NAME));
for(int i = 0;i<cursor.getColumnCount();i++)
    Log.d("ContactInfo", "列"+i+" （"+cursor.getColumnName(i)+"）值为"+cursor.getString(i));
Cursor phoneCursor = cr.query(ContactsContract.CommonDataKinds.Phone.CONTENT_URI, null, ContactsContract.CommonDataKinds.Phone.CONTACT_ID + "= ? ", new String[]{id}, null);
Log.i("ContactInfo", "联系人：" + name);
while (phoneCursor.moveToNext()) {
    String phoneNum = phoneCursor.getString(phoneCursor.getColumnIndex(ContactsContract.CommonDataKinds.Phone.NUMBER));
    Log.i("ContactInfo", "    电话号码：" + phoneNum);
    phoneCursor.close();
}
cursor.close();
```

