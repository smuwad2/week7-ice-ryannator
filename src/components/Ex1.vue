<script>
    export default { 
        // Add Code Here to complete the task
        // Note: DO NOT USE "eval()". In security, "eval" is considered "evil"!!!
        data() {
            return {
                x: 0,
                y: 0,
                result: 0,
                selectedOp: '+', 
                operators: ['+', '-', '*', '/', '%'] 
            };
        },
        computed: {
            result() {
                const x = Number(this.x);
                const y = Number(this.y);
                switch (this.selectedOp) {
                    case '+': return x + y;
                    case '-': return x - y;
                    case '*': return x * y;
                    case '/': return x / y;     // tests never divide by 0
                    case '%': return x % y;     // tests never mod by 0
                    default:  return '';
                }
            }
        }
    }
</script>

<template>
    <p>x <input v-model.number="x"></p>
    <select v-model="selectedOp">
        <option v-for="op in operators" >{{ op }}</option>
    </select>
    <p>y <input v-model.number="y"></p>
    
    <p>---------------------</p>

    <p>= {{result}}</p>
</template>

<style scoped>
    p, input { font-family: monospace; }
    p { white-space: pre; }

</style>
