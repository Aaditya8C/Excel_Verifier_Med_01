<template>

    <div class="mainFile">
        <div class="file">
            <!-- {{ fileData }} -->
            <table id="excelData">
                <thead id="headings">
                    <tr>
                        <th v-for="item in headers" :key="item" class="tHead">
                            {{ item }}
                        </th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="i in fileData.length" :key="i">
                        <td v-for="j in fileData[i]" :key="j" id="one">
                            {{ j }}
                        </td>
                    </tr>
                </tbody>
            </table>

        </div>
    </div>

    <div class="main-btn">
        <div class="first">
            <input type="file" id="fBtn" v-on:change="addFile" placeholder="Choose XL File" />
        </div>
        <div class="second">
            <!-- <button id="sBtn" v-on:click="isShownErr = !isShownErr">Verify XL File</button> -->
            <button id="sBtn" v-on:click="showErrorTable">Verify XL File</button>
        </div>

        <excelExport :fileData="fileData" :file1="file1" />
    </div>

    <div class="rowsAffect1">
        <div class="rowsAffect">
            <p>Affected Rows: {{ rowsAffected }}</p>
            <p>Correct Rows: {{ rowsCorrect }}</p>
        </div>
    </div>

    <div v-show="isShownErr" class="mainError">
        <!-- <div class="err1">
            <ul class="errHeadings">
                <li class="el2">Row No.</li>
                <li class="el2">Name of field</li>
                <li class="el2">Error</li>
                <li class="el2">Error Description</li>
            </ul>
        </div> -->
        <div class="errorDetails">
            <div class="errList">
                <ul class="el1">
                    <li class="el2">Row No</li>
                    <li v-for="h in errRowIndex" :key="h">
                        {{ h }}
                    </li>
                </ul>
            </div>
            <div class="errList">
                <ul class="el1">
                    <li class="el2">Name</li>
                    <li v-for="i in errHead" :key="i">
                        {{ i }}
                    </li>
                </ul>
            </div>
            <div class="errList">
                <ul class="el1">
                    <li class="el2">Error</li>
                    <li v-for="(j, a) in errData" :key="j" id="er"
                        v-on:click="showPrev(j, errRowIndex[a], errHead[a], errCodes[a])">
                        {{ j }}
                    </li>
                </ul>
            </div>
            <div class="errList">
                <ul class="el1" id="desc">
                    <li class="el2">Error Description</li>
                    <li v-for="k in errDesc" :key="k">
                        {{ k }}
                    </li>
                </ul>
            </div>
        </div>
    </div>


    <!-- Popup Model -->
    <div class="basePop" v-show="isPop">
        <div class="mainPop">
            <div class="firstPop">
                <div class="tBtn1C">
                    <button class="tBtn1" v-on:click=closePop>X</button>
                </div>
                <p>Previous Data: </p>
                <p class="ip"> {{ this.tempErr }}</p>
                <p>Enter new Data</p>
                <input type="text" class="ip" v-model='tempResolve' v-show='userDrop'
                    placeholder="Enter correct data" />
                <input type="email" class="ip" v-model='tempResolve' v-show='emailDrop'
                    placeholder="Enter correct data" />
                <input type="password" class="ip" v-model='tempResolve' v-show='passDrop'
                    placeholder="Enter correct data" />
                <input type="number" class="ip" v-model='tempResolve' v-show='phoneDrop'
                    placeholder="Enter correct data" />
                <select id="f" class="ipD" v-show="countryDrop" v-model="tempResolve">
                    <option>Select</option>
                    <option v-for="code in callingCodes" :key="code" :value='code.value'>{{ code.label }}</option>
                </select>
                <select id="f" class="ipD" v-show="stateDrop" v-model="tempResolve">
                    <option>Select</option>
                    <option v-for="s in stateNames1[tempIndex - 1]" :key="s" :value='s'>{{ s }}</option>
                </select>
                <select id="f" class="ipD" v-show="cityDrop" v-model="tempResolve">
                    <option>Select</option>
                    <option v-for="c in cityNames1[tempIndex - 1]" :key="c" :value='c'>{{ c }}</option>
                </select>

                <button class="tBtn" v-on:click="saveResolve">Save Data</button>
            </div>
        </div>
    </div>

</template>
<script>
// import readXlsxFile from 'read-excel-file'
// import moment from 'moment'
import * as XLSX from 'xlsx'
import excelExport from './excelExport.vue';
import countryCodes from 'country-codes-list';
import { City, State } from 'country-state-city';
// import { Country, State, City } from 'country-state-city';
export default {
    name: 'excelImport',
    components: {
        excelExport
    },
    methods: {
        // for marking errors with red underline ------Incomplete
        exportFile() {
            alert("Hello")
        },
        showErrorTable() {
            if (this.errData.length > 0) {
                this.isShownErr = !this.isShownErr
                alert("Errors have been detected and displayed below")
            }
            else {
                alert("Excel file doesn't contain any errors")
            }
        },
        addFile(e) {


            this.countryCodeList = countryCodes.customList('countryCode', '{countryCallingCode}')
            this.file = e.target.files[0];
            // this.callingCodes.push(Object.values(this.countryCodeList))
            Object.values(this.countryCodeList).map((v) => {
                this.callingCodes.push({ label: v, value: v })
            })
            // console.log(this.callingCodes);

            // this.allStateList = State.getAllStates()
            // // console.log("States:",this.allStateList[0].name);
            // // console.log("States:",this.allStateList);
            // for (let s = 0; s < this.allStateList.length; s++) { 
            //     this.allStateNames.push({label: this.allStateList[s].name,value: this.allStateList[s].name});
            // }
            // console.log(this.stateNames);




            // console.log("All state names:",this.allStateNames);
            // let keysCode = [];
            // for (let i = 0; i < codessss.length; i++) {
            //     keysCode.push( this.getKeyByValue(this.countryCodeList, codessss[i]))

            // }
            // // console.log("Codesss", this.getKeyByValue(this.countryCodeList, codessss));
            // console.log("Codessss",keysCode);
            //fileReader function
            const reader = new FileReader();
            reader.onload = (e) => {
                const data = e.target.result;
                const wb = XLSX.read(data, { type: 'binary' });

                //methods
                const sname = wb.SheetNames[0];
                const ws = wb.Sheets[sname];
                this.fileData = XLSX.utils.sheet_to_json(ws, { header: 1 })
                this.rowsCorrect = this.fileData.length;
            }
            reader.readAsBinaryString(this.file);

        },
        //Method for converting names of header into snake case
        // snake_case_string(str) {
        //     return str && str.match(/[A-Z]{2,}(?=[A-Z][a-z]+[0-9]|\b)|[A-Z]?[a-z]+[0-9]|[A-Z]|[0-9]+/g)
        //         .map(s => s.toLowerCase())
        //         .join('_');
        // },
        toSnakeCase(str = '') {
            const strArr = str.split(' ');
            const snakeArr = strArr.reduce((acc, val) => {
                return acc.concat(val.toLowerCase());
            }, []);
            return snakeArr.join('_');
        },
        //Method for validating gender column
        validate() {
            this.errData = [];
            this.errHead = [];
            this.errDesc = [];
            this.errRowIndex = [];
            this.stateCodes = [];
            this.stateNames2 = [];
            this.stateCodes1 = [];
            this.cityDetails = [];
            this.errCodes = [];
            // this.errColName = [];
            // this.cityNames1 = [];
            this.newData.forEach((value) => {
                Object.keys(value).map(k => {
                    if (k == 'name') {
                        // console.log("Type of name ", typeof (value[k]));
                        if (/\d/.test(value[k])) {
                            // console.log(value[k], "is incorrect name");
                            this.errData.push(value[k])
                            this.errDesc.push("First name must be String")
                            this.errHead.push(k)
                            this.errRowIndex.push(value.no.toString())
                        }
                        if (this.validateNames(value[k])) {
                            // console.log(value[k], "Error in name");
                            this.errData.push(value[k])
                            this.errDesc.push("First name must not contain special characters")
                            this.errHead.push(k)
                            this.errRowIndex.push(value.no.toString())

                        }
                    }
                    if (k == 'email') {
                        if (!this.validateEmail(value[k])) {
                            // console.log(value[k], "Invalid email");
                            this.errData.push(value[k])
                            this.errDesc.push("Email address format is invalid")
                            this.errHead.push(k)
                            this.errRowIndex.push(value.no.toString())

                        }
                    }
                    if (k == 'phone_number') {
                        if (!/\d/.test(value[k]) || !/[0-9]+[0-9]+[0-9]+[0-9]+[0-9]+[0-9]+[0-9]+[0-9]+[0-9]+[0-9]/.test(value[k])) {
                            // console.log(value[k], "Phone No must be numeric");
                            this.errData.push(value[k])
                            this.errDesc.push("Phone No must be numeric")
                            this.errHead.push(k)
                            this.errRowIndex.push(value.no.toString())
                        }
                    }
                    if (k == 'country_code') {
                        if (!Object.values(this.countryCodeList).includes(value[k].toString()) || !/\d/.test(value[k])) {
                            // console.log(typeof (value[k]));
                            // console.log(value[k], "is not a valid country code");
                            this.errData.push(value[k])
                            this.errDesc.push("It is not a valid country code")
                            this.errHead.push(k)
                            this.errRowIndex.push(value.no.toString())
                        }
                    }
                    if (k == 'state') {
                        this.stateList = [];
                        this.stateNames1 = [];
                        this.stateNames2 = [];
                        this.stateCodes = [];
                        this.errCodes.push(this.codeValPair[value.no - 1])
                        // this.cityDetails = [];
                        // this.cityDetails = [];
                        for (let c = 0; c < this.codeValPair.length; c++) {
                            this.stateList.push(State.getStatesOfCountry(this.codeValPair[c]));
                        }
                        for (let n = 0; n < this.stateList.length; n++) {
                            for (let m = 0; m <= this.stateList[n].length; m++) {
                                if (m == this.stateList[n].length) {
                                    this.stateNames1.push(Object.values(this.stateNames))
                                    // this.stateCodes1.push(Object.values(this.stateCodes))
                                    this.stateNames = [];
                                    // console.log(this.stateNames1);
                                }
                                else {
                                    this.stateNames.push(this.stateList[n][m].name);
                                    if (!this.stateCodes.includes(this.stateList[n][m].isoCode))
                                        this.stateCodes.push(this.stateList[n][m].isoCode);
                                }
                            }
                        }

                        if (typeof (value[k]) != typeof ('')) {
                            // console.log(value[k], "is incorrect name");
                            this.errData.push(value[k])
                            this.errDesc.push("State name must be String")
                            this.errHead.push(k)
                            this.errRowIndex.push(value.no.toString())
                        }
                        else {
                            for (let n = 0; n < this.stateList.length; n++) {
                                for (let m = 0; m < this.stateList[n].length; m++) {
                                    if (!this.stateNames2.includes(this.stateList[n][m].name))
                                        this.stateNames2.push(this.stateList[n][m].name);
                                }
                            }

                            for (let g = 0; g < this.stateCodes.length; g++) {
                                if (value[k] == this.stateNames2[g]) {
                                    this.stateCodes1.push(this.stateCodes[g])
                                }
                                else if (!this.stateNames2.includes(value[k])) {
                                    // console.log("value:" + value[k]);
                                    this.stateCodes1.push('undefined');
                                    break;
                                }
                            }

                            // console.log(cout);
                            if (!Object.values(this.stateNames1[value.no - 1]).includes(value[k].toString())) {
                                // console.log(Object.values(this.stateNames1[value.no - 1]));

                                // console.log("nnnnnnnn",this.codeValPair[value.no - 1]);
                                console.log(value[k], "is not a state");
                                this.errData.push(value[k])
                                this.errDesc.push("State doesn't belong to the given country code")
                                this.errHead.push(k)
                                this.errRowIndex.push(value.no.toString())
                                // this.errCodes.push(this.codeValPair[value.no - 1])
                            }
                        }
                        if (this.validateNames(value[k])) {
                            // console.log(value[k], "Error in name");
                            this.errData.push(value[k])
                            this.errDesc.push("State name must not contain special characters")
                            this.errHead.push(k)
                            this.errRowIndex.push(value.no.toString())
                        }
                        // console.log("bbbbbb", this.stateCodes1[value.no - 1]);
                        // console.log(this.codeValPair[value.no - 1]);
                        // this.cityDetails.push(City.getCitiesOfState(this.codeValPair[value.no - 1], this.stateCodes1[value.no - 1]))
                        // console.log("Cities", this.cityDetails);

                    }
                    if (k == 'city') {
                        if (typeof (value[k]) != typeof ('')) {
                            // console.log(value[k], "is incorrect name");
                            this.errData.push(value[k])
                            this.errDesc.push("City Name must be String")
                            this.errHead.push(k)
                            this.errRowIndex.push(value.no.toString())
                        }
                        if (this.validateNames(value[k])) {
                            // console.log(value[k], "Error in name");
                            this.errData.push(value[k])
                            this.errDesc.push("City name must not contain special characters")
                            this.errHead.push(k)
                            this.errRowIndex.push(value.no.toString())
                        }

                    }

                    if (k == 'username') {
                        if (!this.validateEmail(value[k])) {
                            // console.log(value[k], "Invalid email");
                            this.errData.push(value[k])
                            this.errDesc.push("Username format is invalid")
                            this.errHead.push(k)
                            this.errRowIndex.push(value.no.toString())
                        }
                    }
                    // if (k == 'password') {
                    //     // this.vaidateDate(value[k])


                    //     // if (!moment(value[k], "DD/MM/YYYY", true).isValid()) {
                    //     //     // console.log(value[k], "Invalid date");
                    //     //     this.errData.push(value[k])
                    //     //     this.errDesc.push("Date must be in the format: DD/MM/YYYY")
                    //     //     this.errHead.push("Date")
                    //     // }
                    // }

                })
            })
            console.log(this.errCodes);
            // console.log("Errors: ", this.errData);
            // console.log("Index", this.errRowIndex);
            // console.log(City.getCitiesOfState("IN", "DL"));

            //To retrieve list of states according to country code in xl sheet columns

        },
        validateEmail(email) {
            return String(email)
                .toLowerCase()
                .match(
                    /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
                );
        },
        // Method for checking special characters in string not running
        validateNames(name) {
            return String(name).toLowerCase().match(/^[!@#$%^&*()_+\-=[\]{};':"\\|,.<>/?]*$/);
        },
        showPrev(item, i, k) {
            this.tempErr = item;
            // console.log("colllll :  ",col)
            this.col = k
            this.tempIndex = i
            if (this.col == 'name')
                this.userDrop = true;
            if (this.col == 'email') {
                this.emailDrop = true
            }
            if (this.col == 'phone_number') {
                this.phoneDrop = true
            }
            if (this.col == 'city') {
                this.cityDrop = true
            }
            if (this.col == 'state') {
                this.stateDrop = true
            }
            if (this.col == 'country_code') {
                this.countryDrop = true
            }
            if (this.col == 'username') {
                this.emailDrop = true
            }
            if (this.col == 'password') {
                this.passDrop = true
            }

            this.isPop = !this.isPop;
        },
        // For saving correct data in popup
        saveResolve() {
            console.log("col", this.col);
            console.log(this.tempResolve);
            if (this.tempResolve == this.tempErr) {
                alert("Enter new value")
            }
            else if (this.tempResolve == null) {
                alert("Please enter some value")
            }
            else if (this.col == 'name') {
                if (/\d/.test(this.tempResolve)) {
                    console.log(this.tempResolve);
                    // console.log(value[k], "is incorrect name");
                    alert("numbers not allowed in name");
                    // this.errData.push()
                    // this.errDesc.push("First name must be String")
                    // this.errHead.push(k)
                    // this.errRowIndex.push(value.no.toString())
                }
                else
                    this.modify();
            }
            else if (this.col == 'email' || this.col == 'username') {
                if (!this.validateEmail(this.tempResolve)) {
                    if (this.col == 'email')
                        alert("Email format is invalid");
                    if (this.col == 'username')
                        alert("Username format is invalid");
                }
                else
                    this.modify();
            }
            else if (this.col == 'phone_number') {
                if (!/\d/.test(this.tempResolve) || !/[0-9]+[0-9]+[0-9]+[0-9]+[0-9]+[0-9]+[0-9]+[0-9]+[0-9]+[0-9]/.test(this.tempResolve)) {
                    console.log(this.tempResolve);
                    alert("invalid phone number");
                }
                else
                    this.modify();
            }
            else if (this.col == 'country_code') {
                this.modify();
            }
            else if (this.col == 'city') {
                this.modify();
            }
            else if (this.col == 'state') {
                if (/\d/.test(this.tempResolve)) {
                    console.log(this.tempResolve);
                    alert("numbers not allowed in state name");
                }
                else
                    this.modify();
            }
            this.tempResolve = null;
        },
        getKeyByValue(object, value) {
            return Object.keys(object).find(key => object[key] === value);
        },
        modify() {
            this.userDrop = false;
            this.emailDrop = false;
            this.phoneDrop = false;
            this.passDrop = false;
            this.stateDrop = false;
            this.isPop = !this.isPop;
            for (let i = 1; i < this.fileData.length; i++) {
                for (let j = 1; j < this.fileData.length; j++) {
                    if (this.fileData[i][j] == this.tempErr && i == this.tempIndex) {
                        this.fileData[i][j] = this.tempResolve
                        break;
                    }
                    // break;
                }
            }
            alert("Information saved successfully")
        },
        closePop() {
            this.userDrop = false;
            this.emailDrop = false;
            this.phoneDrop = false;
            this.passDrop = false;
            this.stateDrop = false;
            this.countryDrop = false;
            this.cityDrop = false;
            this.isPop = !this.isPop;
        }
    },
    beforeUpdate() {
        this.codeValPair = [];
        this.headers = this.fileData[0];
        let final_data = [];
        for (let index = 1; index < this.fileData.length; index++) {
            let data = this.fileData[index];
            let obj = {};
            Object.keys(this.headers).map(f => {
                obj[this.toSnakeCase(Object.values(this.headers)[f])] = data[f]
            })
            final_data.push(obj);
        }
        this.newData = final_data;



        //For getting country codes in xl sheet
        this.newData.forEach((value) => {
            Object.keys(value).map(k => {
                if (k == 'country_code') {
                    // this.xlCode.push(value[k]);
                    this.codeValPair.push(this.getKeyByValue(this.countryCodeList, value[k].toString()));
                    this.xlCode.push(value[k]);
                }
            }
            )
        })



        this.validate();

        this.newData.forEach((value) => {
            Object.keys(value).map(k => {
                if (k == 'city') {
                    this.cityNames1 = [];
                    this.cityDetails.push(City.getCitiesOfState(this.codeValPair[value.no - 1], this.stateCodes1[value.no - 1]))
                    for (let n = 0; n < this.cityDetails.length; n++) {
                        for (let m = 0; m <= this.cityDetails[n].length; m++) {
                            if (m == this.cityDetails[n].length) {
                                this.cityNames1.push(Object.values(this.cityNames));
                                this.cityNames = [];
                            }
                            else {
                                this.cityNames.push(this.cityDetails[n][m].name);
                            }
                        }
                    }

                    if (!this.cityNames1[value.no - 1].includes(value[k])) {
                        // console.log(value[k], "is not a city");
                        this.errData.push(value[k])
                        this.errDesc.push("City not belongs to given state")
                        this.errHead.push(k)
                        this.errRowIndex.push(value.no.toString())

                    }
                }
            }
            )
        })

        // this.validate();

        this.rowsAffected = 0;
        this.rowsCorrect = this.fileData.length;
        for (let i = 1; i < this.fileData.length; i++) {
            for (let j = 0; j < this.errData.length; j++) {
                if (this.fileData[i].includes(this.errData[j]) && i == this.errRowIndex[j]) {
                    this.rowsAffected++;
                    break;
                }
            }
        }
        console.log("Errorsssss: ", this.rowsAffected);
        this.rowsCorrect -= this.rowsAffected + 1

        //For counting the affected rows
        // this.rowsAffected = 0;
        // this.rowsCorrect = this.fileData.length;
        // for (let i = 1; i < this.fileData.length; i++) {
        //     for (let j = 0; j < this.errData.length; j++) {
        //         if (this.fileData[i].includes(this.errData[j])) {
        //             this.rowsAffected++;
        //             break;
        //         }
        //     }
        // }
        // console.log("Errorsssss: ",this.rowsAffected);
        // this.rowsCorrect -= this.rowsAffected + 1
        // console.log(this.cityNames1);
    },

    data() {
        return {
            file: File,
            fileData: [],
            headers: [],
            newData: [],
            errData: [],
            errDesc: [],
            errHead: [],
            index: null,
            mkErr: false,
            isShownErr: false,
            isPop: false,
            tempErr: null,
            tempResolve: null,
            col: null,
            file1: File,
            rowsAffected: 0,
            rowsCorrect: 0,
            countryCodeList: {},
            stateList: [],
            allStateList: {},
            allStateNames: [],
            codeValPair: [], //country codes (IN,BO,etc) 
            stateNames: [],
            stateNames1: [], //Only state names
            stateNames2: [],
            xlCode: [],
            errRowIndex: [], // rows of elements in errData
            errCodes: [],
            cityDetails: [],
            stateCodes: [], //for state or iso codes
            stateCodes1: [],
            cityNames: [],
            cityNames1: [],
            tempIndex: null,
            userDrop: false,
            emailDrop: false,
            phoneDrop: false,
            cityDrop: false,
            stateDrop: false,
            countryDrop: false,
            passDrop: false,
            callingCodes: [],
        }
    }

}
</script>
<style>
.rowsAffect1 {
    display: flex;
    justify-content: center;
    margin-top: 5rem;

}

.rowsAffect {
    border-radius: 10px;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    background-color: #75E6DA;
    font-size: 1.2rem;
    width: 20rem;
    font-weight: bold;
    box-shadow: 1px 3px 5px 2px;
}

.mRed {
    background-color: red;
}

.basePop {
    right: 0;
    top: 0;
    left: 0;
    bottom: 0;
    backdrop-filter: blur(5px);
    position: fixed;
    display: flex;
    justify-content: center;
    align-items: center;
}

.ip {
    display: flex;
    align-items: center;
    justify-content: center;
    background-color: white;
    width: 10rem;
    height: 2rem;
    border-radius: 10px;
    border: 1px solid black;

}

.ipD {
    display: flex;
    align-items: center;
    justify-content: center;
    background-color: white;
    width: 10rem;
    height: 2rem;
    border-radius: 10px;
    margin-bottom: 2rem;
    border: 1px solid black;
}

.mainPop {
    background-color: #D4F1F4;
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 22rem;
    height: fit-content;
    /* height: 23rem; */
    box-shadow: 2px 4px 10px 1px;
    border-radius: 35px;
}

.firstPop {
    font-size: 1rem;
    margin-top: .5rem;
    font-weight: 700;
}

.tBtn {
    margin-bottom: 1rem;
    background-color: #75E6DA;
    font-size: 1rem;
    font-weight: bolder;
    padding: 10px;
    font-family: sans-serif;
    cursor: pointer;
    border-radius: 10px;
    border: none;
    font-weight: 600;
}

.tBtn1 {
    background-color: #75E6DA;
    font-size: 1rem;
    font-weight: bolder;
    padding: 10px;
    font-family: sans-serif;
    cursor: pointer;
    border-radius: 10px;
    border: none;
    font-weight: 600;
}

.tBtn1C {
    display: flex;
    justify-content: end;
}

.tBtn1:hover {
    text-align: right;
    background-color: #21B6A8;
    cursor: pointer;
    color: beige;
    transition-duration: .3s;
    color: blanchedalmond;

}

.tBtn:hover {
    background-color: #21B6A8;
    cursor: pointer;
    color: beige;
    transition-duration: .3s;
    color: blanchedalmond;

}

.tBtn1:active {
    background-color: #75E6DA;
    color: black;
    transition-duration: .3s;
}

.tBtn:active {
    background-color: #75E6DA;
    color: black;
    transition-duration: .3s;
}

.mainError {
    width: 82rem;
    margin-top: 6rem;
}

.errList {
    flex: auto;
}
/* .err1{
    flex: auto;
} */

.el2 {
    background-color: #21B6A8;
    color: blanchedalmond;
    font-weight: 700;
}

#er {
    cursor: pointer
}

.errHeadings {
    font-weight: bold;
    font-size: 1.2rem;
    display: flex;
    list-style: none;
}

.el1 {
    flex: 1;
    width: 100%;
}
#desc{
    font-size: 1rem;
}

#one {
    padding: .5rem;
}

.errHeadings li {
    border: 1px solid black;
    background-color: #21B6A8;
    color: blanchedalmond;
}
.errHeadings{
    flex: auto;
    height: 2rem;
}

.errorDetails {
    display: flex;
    justify-content: space-between;
}

.errorDetails li {
    border: 1px solid black;
    list-style: none;
    padding: .8rem;
}
.tHead {
    background-color: #21B6A8;
    color: blanchedalmond;
}



th,
td {
    width: 90px;
    border: .5px solid black;
}

table {
    width: 100%;
    border-collapse: collapse;
}

.mainFile {
    display: flex;
    justify-content: center;
    align-items: center;
}

.file {
    width: 55rem;
    height: 25rem;
    border: dotted 3px black;
    padding: 2rem;
    overflow-y: scroll;
    overflow-x: scroll;

}

.main-btn {
    margin-top: 2rem;
    display: flex;
    justify-content: space-between;
    align-items: center;
    width: 50rem;
    padding-left: 18rem;
}


#fBtn {
    /* margin-top: 20rem; */
    background-color: #75E6DA;
    font-size: 1.2rem;
    font-weight: bolder;
    border-radius: 10px;
    /* height: 2rem; */
    padding: 9px;
    font-family: sans-serif;
}


#fBtn::-webkit-file-upload-button {
    display: none;
}

#sBtn {
    background-color: #75E6DA;
    font-size: 1.2rem;
    font-weight: bolder;
    border-radius: 10px;
    /* height: 2rem; */
    padding: 10px;
    border: none;
    /* box-shadow: 2px 2px 10px 1px; */
    font-family: sans-serif;
}

#fBtn:hover {
    background-color: #21B6A8;
    cursor: pointer;
    color: beige;
    transition-duration: .3s;
}

#sBtn:hover {
    background-color: #21B6A8;
    cursor: pointer;
    color: beige;
    transition-duration: .3s;
}

#fBtn:active {
    background-color: #75E6DA;
    color: black;
    transition-duration: .3s;
}

#sBtn:active {
    background-color: #75E6DA;
    color: black;
    transition-duration: .3s;
}



th,
tr {
    font-style: bold;
}
</style>