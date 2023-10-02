the code contains a counter called the "id" counter that is sha-256 encrypted and then that id itself is being used in the url:

##########

const state = { id: 0 };

const notes = new Map();

const add = (note) => {

const id = crypto

.createHash('sha256')

.update(state.id.toString())

.digest('hex');

state.id += 1;

notes.set(id, note);

return id;

#############

[https://notes-1.challs.wreckctf.com/view/<id>](https://notes-1.challs.wreckctf.com/view/<id>)

so when we create a new note the id increases and then is hashed and made a new url , so the admin must have id = 0 i.e in sha-256 0 is :

5feceb66ffc86f38d952786c6d696c79c2dbc239dd4e91b46729d73a27fb57e9

so the url becomes:

[https://notes-1.challs.wreckctf.com/view/5feceb66ffc86f38d952786c6d696c79c2dbc239dd4e91b46729d73a27fb57e9](https://notes-1.challs.wreckctf.com/view/5feceb66ffc86f38d952786c6d696c79c2dbc239dd4e91b46729d73a27fb57e9)