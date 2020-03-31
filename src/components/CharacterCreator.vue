<template>
    <div class="characterGenerator">
        <div class="backButton"> 
            <button @click="back">
                ⬅️ 
            </button>
        </div>
        <div class="creator">
            <h1>Create your character</h1>

            <h2>1. Select a standard race</h2>

            <ul>
                <li v-for="race in races" v-bind:key="race.name" v-bind:id="race.name" @click="selectRace(race)">
                    {{ race.name }} ({{ race.points }})
                </li>
            </ul>
            <h2>
                2. Select equipment and Skills
            </h2>
            <div class="skills">
                <h3>
                    Skills
                </h3>
                <ul>
                    <li v-for="skill in skills" v-bind:key="skill.name" v-bind:id="skill.name" @click="selectSkill(skill)">
                        {{ skill.name }} ({{ skill.level }}) <br/>Cost {{ skill.levels[skill.level] }}
                    </li>
                </ul>
                <br/>
                <h3>Item material: </h3>
                <ul>
                    <li @click="selectMaterial('Wd')">
                        Wood
                    </li>
                    <li @click="selectMaterial('Lt')">
                        Leather
                    </li>
                    <li @click="selectMaterial('Cu')">
                        Copper
                    </li>
                    <li @click="selectMaterial('Br')">
                        Bronze
                    </li>
                    <li @click="selectMaterial('Fe')">
                        Iron
                    </li>
                    <li @click="selectMaterial('Ag')">
                        Silver
                    </li>
                    <li @click="selectMaterial('St')">
                        Steel
                    </li>
                    <li @click="selectMaterial('Pt')">
                        Platinum
                    </li>
                    <li @click="selectMaterial('Ad')">
                        Adamantium
                    </li>
                </ul>
                <h3>
                    {{ material }} Items: 
                </h3>
                <ul class="item-list">
                    <li v-for="item in itemList" v-bind:key="item.name" v-bind:id="item.name" @click="selectItem(item)">
                        {{ item.name }} ({{ item.actualCost }})
                    </li>
                </ul>
            </div>
        </div>

        
        <div class="character">
            <div class="char-content">
                <h3>Your character:</h3>
                <h4>{{ character.race }} Points: {{ points }}</h4>
                <h4><br/>Your points {{ character.points }}</h4>
                <h4><br/>Skills:</h4>
                <ul>
                    <li v-for="skill in character.skills" v-bind:key="skill.name" @click="lessSkill(skill)">
                        {{ skill.name }} ({{skill.level}})
                    </li>
                </ul>
                <h4><br />Items:</h4>
                <ul>
                    <li v-for="item in character.items" v-bind:key="item.name" @click="removeItem(item)">
                        {{ item.name }} ({{ item.cost }})
                    </li>
                </ul>
            </div>
        </div>
    </div>
</template>

<script> 
    export default {
        name: "CharacterCreator",
        methods: {
            //Utility functions:
            getIndexByName(arr, name){
                const index = arr.findIndex(x => x.name === name);
                return index;
            },
            back() {
                this.$emit("back");
            },

            //App functions
            removeSelected() {
                this.points = 0;
                for ( let i = 0; i < this.races.length; i++ ) {
                    let el = document.getElementById(this.races[i].name);
                    el.classList.remove("selected");
                }
            },
            selectRace(race) {
                this.removeSelected();
                this.points = race.points;
                this.character.race = race.name;
                this.character.points = this.points;
                let el = document.getElementById(race.name);
                el.classList.toggle("selected");
                this.character.points = this.points + this.character.negPoints; 
            },

            //-------------SKILL LOGIC--------------------------------- //
            selectSkill(skill) {
                // Only add the skill if the player have enough points
                if (this.character.points > skill.levels[skill.level]){
                    // Update the points
                    this.character.negPoints -= skill.levels[skill.level];
                    this.character.points = this.points + this.character.negPoints; 
                    skill.level++;
                    // Add the skill to the skills array of the player
                    for (let i = 0; i < this.character.skills.length; i++) {
                        if (this.character.skills[i].name === skill.name) {
                            this.character.skills[i].level++;
                            return; 
                        }
                    }
                    this.character.skills.push({name: skill.name, level: skill.level});
                }
            },
            lessSkill(skill) {
                skill.level--;
                // Downgrade the level of the skill in the skill's list
                const element = this.getIndexByName(this.skills, skill.name);
                this.skills[element].level--;
                // Remove skill from the list if level is 0
                const index = this.getIndexByName(this.character.skills, skill.name);
                if (skill.level == 0) {
                    this.character.skills.splice(index, 1);
                }
                // Give points to the player
                this.character.negPoints += this.skills[element].levels[this.skills[element].level];
                console.log(element);
                this.character.points = this.points + this.character.negPoints;
            },

            // --------------ITEMS LOGIC------------------------------------- //
            selectMaterial(material) {
                const costs = this.materialCost;
                let newItems = this.items.filter(item => {
                    let isValidMaterial = item.invMaterials.find(el => el === material);
                    if (!isValidMaterial) {return item}
                });

                this.itemList = newItems.map(function(el) {
                    let newEl = el;
                    newEl.actualCost = newEl.cost * costs[material];
                    return newEl
                });
                this.material = material;
            },
            selectItem(item) {
                // Only add the item if the player have enough points
                if (this.character.points > item.actualCost){
                    // Update the points
                    this.character.negPoints -= item.actualCost;
                    this.character.points = this.points + this.character.negPoints; 
                    // Add the item to the items array of the player
                    this.character.items.push({name: item.name, cost: item.actualCost});
                }
            },
            removeItem(item) {
                //Give points back
                this.character.negPoints += item.cost;
                this.character.points = this.points + this.character.negPoints;
                //Remove item from array
                const index = this.getIndexByName(this.character.items, item.name);
                this.character.items.splice(index, 1);
            }
        },
        data() {
            return {
                points: 0,
                itemList: [],
                material: "",
                materialCost: {
                    "Wd": 0.75,
                    "Lt": 0.5,
                    "Cu": 1.25,
                    "Br": 2,
                    "Fe": 2,
                    "Ag": 3,
                    "St": 4,
                    "Pt": 4,
                    "Ad": 12,
                },
                races: [
                    {
                        name: "Gremlin",
                        points: 310
                    },
                    {
                        name: "Dark / Mountain Gnome",
                        points: 290
                    },
                    {
                        name: "Kobold",
                        points: 280
                    },
                    {
                        name: "Goblin",
                        points: 220
                    },
                    {
                        name: "Dwarf",
                        points: 220
                    },
                    {
                        name: "Elf",
                        points: 220
                    },
                    {
                        name: "Human",
                        points: 210
                    },
                    {
                        name: "Minotaur",
                        points: 120
                    },
                    {
                        name: "Troll",
                        points: 120
                    },
                    {
                        name: "Yeti",
                        points: 100
                    },
                    {
                        name: "Sasquatch",
                        points: 100
                    },
                ],
                skills: [
                    {
                        name: "Figther",
                        level: 0,
                        levels: [3, 9, 18, 30, 45, 63, 84, 108, 135, 165, 198, 234, 273, 315]
                    },
                    {
                        name: "Archer",
                        level: 0,
                        levels: [3, 9, 18, 30, 45, 63, 84, 108, 135, 165, 198, 234, 273, 315]
                    },
                    {
                        name: "Axeman",
                        level: 0,
                        levels: [6, 18, 36, 60, 90, 126, 168, 216, 270, 330, 396, 468, 546, 630]
                    },
                    {
                        name: "Swordsman",
                        level: 0,
                        levels: [5, 15, 30, 50, 75, 105, 140, 180, 225, 275, 330, 390, 455, 525]
                    },
                    {
                        name: "Knife User",
                        level: 0,
                        levels: [5, 15, 30, 50, 75, 105, 140, 180, 225, 275, 330, 390, 455, 525]
                    },
                    {
                        name: "Maceman",
                        level: 0,
                        levels: [5, 15, 30, 50, 75, 105, 140, 180, 225, 275, 330, 390, 455, 525]
                    },
                    {
                        name: "Hammerman",
                        level: 0,
                        levels: [5, 15, 30, 50, 75, 105, 140, 180, 225, 275, 330, 390, 455, 525]
                    },
                    {
                        name: "Spearman",
                        level: 0,
                        levels: [5, 15, 30, 50, 75, 105, 140, 180, 225, 275, 330, 390, 455, 525]
                    },
                    {
                        name: "Pikeman",
                        level: 0,
                        levels: [5, 15, 30, 50, 75, 105, 140, 180, 225, 275, 330, 390, 455, 525]
                    },
                    {
                        name: "Lasher",
                        level: 0,
                        levels: [6, 18, 36, 60, 90, 126, 168, 216, 270, 330, 396, 468, 546, 630]
                    },
                    {
                        name: "Crossbowman",
                        level: 0,
                        levels: [5, 15, 30, 50, 75, 105, 140, 180, 225, 275, 330, 390, 455, 525]
                    },
                    {
                        name: "Bowman",
                        level: 0,
                        levels: [5, 15, 30, 50, 75, 105, 140, 180, 225, 275, 330, 390, 455, 525]
                    },
                    {
                        name: "Blowgunner",
                        level: 0,
                        levels: [5, 15, 30, 50, 75, 105, 140, 180, 225, 275, 330, 390, 455, 525]
                    },
                    {
                        name: "Wrestler",
                        level: 0,
                        levels: [4, 12, 24, 40, 60, 84, 112, 144, 180, 220, 264, 312, 364, 420]
                    },
                    {
                        name: "Striker",
                        level: 0,
                        levels: [4, 12, 24, 40, 60, 84, 112, 144, 180, 220, 264, 312, 364, 420]
                    },
                    {
                        name: "Kicker",
                        level: 0,
                        levels: [4, 12, 24, 40, 60, 84, 112, 144, 180, 220, 264, 312, 364, 420]
                    },
                    {
                        name: "Biter",
                        level: 0,
                        levels: [4, 12, 24, 40, 60, 84, 112, 144, 180, 220, 264, 312, 364, 420]
                    },
                    {
                        name: "Dodger",
                        level: 0,
                        levels: [5, 15, 30, 50, 75, 105, 140, 180, 225, 275, 330, 390, 455, 525]
                    },
                    {
                        name: "Shield User",
                        level: 0,
                        levels: [4, 12, 24, 40, 60, 84, 112, 144, 180, 220, 264, 312, 364, 420]
                    },
                    {
                        name: "Armor User",
                        level: 0,
                        levels: [2, 6, 12, 20, 30, 42, 56, 72, 90, 110, 132, 156, 182, 210]
                    },
                    {
                        name: "Thrower",
                        level: 0,
                        levels: [4, 12, 24, 40, 60, 84, 112, 144, 180, 220, 264, 312, 364, 420]
                    },
                    {
                        name: "Swimmer",
                        level: 0,
                        levels: [2, 6, 12, 20, 30, 42, 56, 72, 90, 110, 132, 156, 182, 210]
                    },
                    {
                        name: "Observer",
                        level: 0,
                        levels: [3, 9, 18, 30, 45, 63, 84, 108, 135, 165, 198, 234, 273, 315]
                    },
                    {
                        name: "Discipline",
                        level: 0,
                        levels: [3, 9, 18, 30, 45, 63, 84, 108, 135, 165, 198, 234, 273, 315]
                    },
                    {
                        name: "Climber",
                        level: 0,
                        levels: [3, 9, 18, 30, 45, 63, 84, 108, 135, 165, 198, 234, 273, 315]
                    },
                    {
                        name: "Msc. Object User",
                        level: 0,
                        levels: [3, 9, 18, 30, 45, 63, 84, 108, 135, 165, 198, 234, 273, 315]
                    },
                    
                ],
                items: [
                    {
                        name: "Large Dagger",
                        cost: 16,
                        invMaterials: ["Wd", "Lt"]
                    },
                    {
                        name: "Whip",
                        cost: 28,
                        invMaterials: ["Wd", "Lt"]
                    },
                    {
                        name: "Scourage",
                        cost: 28,
                        invMaterials: ["Wd", "Lt"]
                    },
                    {
                        name: "Scimitar",
                        cost: 20,
                        invMaterials: ["Wd", "Lt"]
                    },
                    {
                        name: "Short Sword",
                        cost: 20,
                        invMaterials: ["Wd", "Lt"]
                    },
                    {
                        name: "Long Sword",
                        cost: 24,
                        invMaterials: ["Wd", "Lt"]
                    },
                    {
                        name: "2-H Sword",
                        cost: 28,
                        invMaterials: ["Wd", "Lt"]
                    },
                    {
                        name: "Battle Axe",
                        cost: 30,
                        invMaterials: ["Wd", "Lt"]
                    },
                    {
                        name: "Helberd",
                        cost: 28,
                        invMaterials: ["Wd", "Lt"]
                    },
                    {
                        name: "Great Axe",
                        cost: 24,
                        invMaterials: ["Wd", "Lt"]
                    },
                    {
                        name: "Mace",
                        cost: 26,
                        invMaterials: ["Wd", "Lt"]
                    },
                    {
                        name: "Flail",
                        cost: 28,
                        invMaterials: ["Wd", "Lt"]
                    },
                    {
                        name: "Warhammer",
                        cost: 26,
                        invMaterials: ["Wd", "Lt"]
                    },
                    {
                        name: "Morningstar",
                        cost: 32,
                        invMaterials: ["Wd", "Lt"]
                    },
                    {
                        name: "Maul",
                        cost: 26,
                        invMaterials: ["Wd", "Lt"]
                    },
                    {
                        name: "Spear",
                        cost: 20,
                        invMaterials: ["Wd", "Lt"]
                    },
                    {
                        name: "Pike",
                        cost: 22,
                        invMaterials: ["Wd", "Lt"]
                    },
                    {
                        name: "Pick",
                        cost: 26,
                        invMaterials: ["Wd", "Lt"]
                    },
                    {
                        name: "Blowgun",
                        cost: 20,
                        invMaterials: ["Wd", "Lt"]
                    },
                    {
                        name: "Bow",
                        cost: 26,
                        invMaterials: ["Wd", "Lt"]
                    },
                    {
                        name: "Crossbow",
                        cost: 26,
                        invMaterials: ["Wd", "Lt"]
                    },
                    {
                        name: "Buckler",
                        cost: 30,
                        invMaterials: ["Lt"]
                    },
                    {
                        name: "Shield",
                        cost: 60,
                        invMaterials: ["Lt"]
                    },
                    {
                        name: "Cap or Mask",
                        cost: 10,
                        invMaterials: []
                    },
                    {
                        name: "Helm",
                        cost: 12,
                        invMaterials: []
                    },
                    {
                        name: "Armour",
                        cost: 24,
                        invMaterials: ["Wd", "Cu", "Br", "Fe", "Ag", "St", "Pt", "Ad"]
                    },
                    {
                        name: "Mail Shirt",
                        cost: 24,
                        invMaterials: ["Wd", "Lt"]
                    },
                    {
                        name: "Breast Plate",
                        cost: 20,
                        invMaterials: ["Wd", "Lt"]
                    },
                    {
                        name: "Leggings",
                        cost: 14,
                        invMaterials: ["Lt"]
                    },
                    {
                        name: "Chain Leggings",
                        cost: 16,
                        invMaterials: ["Wd", "Lt"]
                    },
                    {
                        name: "Greaves",
                        cost: 18,
                        invMaterials: ["Wd", "Lt"]
                    },
                    {
                        name: "Gloves",
                        cost: 16,
                        invMaterials: ["Wd", "Cu", "Br", "Fe", "Ag", "St", "Pt"]
                    },
                    {
                        name: "Gauntlets",
                        cost: 8,
                        invMaterials: ["Wd", "Lt"]
                    },
                    {
                        name: "Low Boots",
                        cost: 8,
                        invMaterials: ["Wd"]
                    },
                    {
                        name: "High Boots",
                        cost: 10,
                        invMaterials: ["Wd"]
                    },
                ],
                character: {
                    race: "",
                    points: 0,
                    negPoints: 0,
                    skills: [],
                    items: []
                }
            }
        },
    }
</script>

<style>
    @import "../assets/sass/characterGenerator.scss";
</style>