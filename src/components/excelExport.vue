<template>
    <div class="third">
        <!-- <button id="sBtn" v-on:click="isShownErr = !isShownErr">Verify XL File</button> -->
        <button id="sBtn" v-on:click="exportF">Submit</button>
        <!-- <a :href="'/demo/sample.xls'" v-on:click="exportF" download>Submit</a> -->

    </div>
</template>
<script>
import xlsExport from 'xlsexport'
import axios from 'axios'
// import { blob } from 'stream/consumers'
export default {
    name: 'excelExport',
    props: {
        exportFile: Function,
        fileData: Array,
    },
    data() {
        return {
            fileinfo: '',
            reader: new FileReader(),
        }
    },
    methods: {

        exportF() {
            let xls = new xlsExport(this.fileData)
            // // console.log(this.fileData);
            let formData = new FormData();
            xls.exportToXLS('file.xls', true)
            // formData.append('file2', xls.exportToXLS('file.xls', true));

            let blob = new Blob([this.fileData], {
                type: "application/csv"
            });
            let fileBlob = new File([blob], 'newFile.csv');
            console.log(fileBlob);

            formData.append("blob", blob, 'demo.csv')

            console.log(formData);
            axios.post('https://httpbin.org/post',
                formData, {
                headers: {
                    'Content-Type': 'multipart/form-data'
                }
            }
            ).then(() => {
                console.log("Successfully posted");
                alert("Your Excel file have been successfully uploaded to our API")
            }).catch((err) => {
                console.log(err);
            })
        }
    }
}
</script>
<style>

</style>