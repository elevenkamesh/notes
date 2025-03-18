```go
func (n  examplestruct ) Validate()(err error ){
	if n.Author !-= "" {
	errs = errors.join(err , fmt.Errorf("author is  empty %s" , n.Author))
	}
		if n.Title !-= "" {
	errs = errors.join(err , fmt.Errorf("Title is  empty %s" , n.Title))
	}
	return err
}
```