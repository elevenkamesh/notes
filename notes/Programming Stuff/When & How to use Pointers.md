**A Pointer is always 8 bytes in size**

***IMPORTANT NOTE:*** Basically `&` gives you address and `*` gives you the value it's pointing to.

**When to use pointers?**
1. We need to update the state
2. When we want to optimise the memory for large objects that are getting called a LOT.

e.g. 
```Go
type User struct {
	email string
	username string
	age int
	file []byte // ?? large
}

// we're just getting the value
// x amount of bytes -> sizeOf(user)
// can be 1gb or even more if file is there and we create a copy hence pointer is better approach
func (u User) Email() string {
	return u.email
}

// 8 bytes
func (u *User) Email() string {
	return u.email
}

// we're updating the value
func (u *User) UpdateEmail(email string) {
	u.email = email
}
```