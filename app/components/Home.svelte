<script>
    import {Template} from 'svelte-native/components'
   
    let todos = []
    
    let dones = [] //completed items go here
		const removeFromList = (list, item) => list.filter(t => t !== item);
		const addToList = (list, item) => [item, ...list]

    let textFieldValue = ""
    const { default: App } = require("~/App.svelte");

    async function onItemTap(args) {
		let result = await action("What do you want to do with this task?", "Cancel", [
				"Mark completed",
				"Delete forever"
		]);

		console.log(result); // Logs the selected option for debugging.
		let item = todos[args.index]
		switch (result) {
				case "Mark completed":
						dones = addToList(dones, item) // Places the tapped active task at the top of the completed tasks.
						todos = removeFromList(todos, item) // Removes the tapped active task.
						break;
				case "Delete forever":
						todos = removeFromList(todos, item) // Removes the tapped active task.
						break;
				case "Cancel" || undefined: // Dismisses the dialog
						break;
		}
	}

    function onButtonTap() {
        if (textFieldValue === "") return;
        console.log("New task added: " + textFieldValue + ".");
            todos = [{ name: textFieldValue }, ...todos]
            textFieldValue = "";
    }

    async function onDoneTap(args) {
        let result = await action ("What do you want to do with this task?", "Cancel", ["Mark To do", 
        "Delete forever"]);

        let item = dones[args.index]
        switch (result) {
            case "Mark To do":
                todos = addToList(todos, item)
                dones = removeFromList (dones, item)
                break;
            case "Delete forever":
                dones = removeFromList (dones, item)
                break;
            case "Cancel" || undefined:
                break;
        }
    }
</script>
<page>
    <actionBar title="My To do list" />

    <tabView>
            <tabViewItem title="To Do">
                    <gridLayout columns="*,120" rows="70,*">
                        <textField col="0" row="0" bind:text="{textFieldValue}" hint="Type new task..." editable="true" on:returnPress="{onButtonTap}" />
                        <button col="1" row="0" text="Add task" on:tap="{onButtonTap}" class="-primary" />

                        <listView items="{todos}" on:itemTap="{onItemTap}" row="1" colSpan="2">
                            <Template let:item>
                                <label text="{item.name}" textWrap="true" />
                            </Template>
                        
                        </listView>
                    </gridLayout>
            </tabViewItem>
            <tabViewItem title="Completed">
                <listView items="{dones}" on:itemTap="{onDoneTap}">
                    <Template let:item>
                            <label text="{item.name}" textWrap="true" class="todo-item-completed"/>
                    </Template>
                </listView>
            </tabViewItem>
    </tabView>
</page>
<style>
	textField {
			font-size: 20;
	}

    label {
				font-size: 18;
}

.todo-item-completed {
	color: #939393;
	text-decoration: line-through;
}
</style>