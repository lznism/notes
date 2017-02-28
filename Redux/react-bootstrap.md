### Button

```html
<Button>default</Button>
<Button bsStyle="primary">primary</Button>
<Button bsStyle="success">success</Button>
<Button bsStyle="info">info</Button>
<Button bsStyle="warning">warning</Button>
<Button bsStyle="danger">danger</Button>
<Button bsStyle="link">link</Button>

<Button bsStyle="primary" bsSize="large">button large</Button>
<Button bsStyle="primary" bsSize="small">button small</Button>
<Button bsStyle="primary" bsSize="xsmall">button xsmall</Button>

<Button bsStyle="success" block>button block</Button>
<Button bsStyle="primary" block>button block</Button>

<Button bsStyle="success" block active>button active</Button>

<Button href="#">这会是一个a链接</Button>

<Button bsStyle="warning" disabled>button disabled</Button>


<ButtonToolbar>
    <ButtonGroup>
        <Button bsStyle="primary">btn1</Button>
        <Button bsStyle="success">btn2</Button>
        <Button bsStyle="warning">btn3</Button>
        <Button bsStyle="danger">btn4</Button>
    </ButtonGroup>
    <ButtonGroup>
        <Button bsStyle="primary">btn1</Button>
        <Button bsStyle="success">btn2</Button>
        <Button bsStyle="warning">btn3</Button>
    </ButtonGroup>  
</ButtonToolbar>

<DropdownButton title="DropDown" id="dropdown">
    <MenuItem eventKey="1">Drop Down Link 1</MenuItem>
    <MenuItem eventKey="2">Drop Down Link 2</MenuItem>
</DropdownButton>

<ButtonGroup vertical>
    <Button>1</Button>
    <Button>2</Button>
    <Button>3</Button>

    <DropdownButton title="dropdown" id="dropdown">
        <MenuItem eventKey="1">1</MenuItem>
        <MenuItem eventKey="2">2</MenuItem>
        <MenuItem eventKey="3">3</MenuItem>
    </DropdownButton>
</ButtonGroup>

<ButtonGroup vertical block>
    <Button>1</Button>
    <Button>2</Button>
</ButtonGroup>
```