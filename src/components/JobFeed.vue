<template>
  <div class="job-feed">
    <div class="logo">
      <img src="@/assets/mojob_logo_on_white.png" alt="logo">
    </div>
    
    <div class="job-listings">
      <div class="filter">
        <div id="dialog" v-if="dialogShow">
          <div v-for="func in positionFunctions" :key="func.id" class="position-function">
            <input type="checkbox" @change="openChildren(func.id, $event)" :id="func.id" :value="func.name">
            <label>{{func.name}}</label><br>
            <div v-if="func.children.length > 0 " class="function-children" :id="'child-'+func.id">
              <div v-for="child in func.children" :key="child.id" class="position-child" >
                <input type="checkbox" :id="child.id" @change="onFilter(child.id, $event)" :value="child.name">
                <label>{{child.name}}</label><br>
              </div>
            </div>
          </div>
          
        </div>
        <div class="functions" @click="showDialog()">
          FILTER BY POSITION <i class="fas fa-caret-down"></i></div>
        

        <div class="pagination">
          <select @change="onPaginationChange($event)" v-model="selectKey">
            <option value=5>5 PER PAGE</option>
            <option value=25>25 PER PAGE</option>
            <option value=2000>DISPLAY ALL</option>
          </select>
        </div>
      </div>
      <div class="jobs">
        <div v-for="job in jobListings" :key="job.id" class="listing">
          <h4>{{job.job.title}}</h4>
          <div>
            <ul class="listing-info">
              <p>{{job.job.unit.display_name}}</p>
              <li>{{job.job.position_function.name_en}}</li>
              <li>{{job.job.due_date}}</li>
            </ul>
          </div>
        </div>
      </div>
    </div>
    
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
import { JobListing, PositionFunction } from '@/models/models';

@Component
export default class JobFeed extends Vue {

  @Prop({ default: () => [], type: Array as () => JobListing[] })
  private jobListings!: JobListing[];
  @Prop({ default: () => [], type: Array as () => PositionFunction[] })
  private positionFunctions!: PositionFunction[];
  public selectKey: number= 5
  public dialogShow: boolean = false;
  public functions: number[] = []



  
  openChildren(id:string, val:any):void{
    if (val.target.checked){
      document.getElementById(`child-${id}`)!.style.display = 'block'
      this.functions.push(+id)
    } else {
      document.getElementById(`child-${id}`)!.style.display = 'none'
    }
  }

  onFilter(id:string, val:any):void{
    if(val.target.checked){
      this.functions.push(+id)

    }
  }

  showDialog():void{
    this.dialogShow = !this.dialogShow
    if(this.dialogShow){
      this.functions = []
    }
    if(!this.dialogShow){
      this.$emit('filterFunc',this.functions)
      
    }
    
  }

  onPaginationChange(val:any): void{
    // const index = 
    this.$emit('changePag', val.target!.value)
  }

  
}
</script>

<style scoped>
  .job-feed{

  }

  .logo{
    height: 66px;
    background-color: white;
    text-align: left;
  }
  img{
    height: 100%;
  }

  .job-listings{
    margin: 0 55px;
  }

  .functions{
    cursor: pointer;
    justify-content: space-between;
    display: flex;
    width: 170px;
    align-items: center;
    letter-spacing: 1.3px;
  }

  .pagination{
    display: flex;
    align-items: center;
  }
  
  #dialog{
    position: absolute;
    top: 45px;
    text-align: left;
    overflow-y: scroll;
    left: 0;
    padding: 17px;
    height: 380px;
    width: 406px;
    z-index: 2;
    background: white;
    box-shadow: 0px 9px 12px rgba(0, 0, 0, 0.14), 0px 3px 16px rgba(0, 0, 0, 0.12), 0px 5px 6px rgba(0, 0, 0, 0.2);
    border-radius: 5px;
  }

  #dialog input{
    margin-bottom: 14px;
  }

  #dialog::-webkit-scrollbar {
    display: none;
  }
  .function-children{
    margin-left:30px ;
    display: none;
  }

  .listing-info{
    display: flex;
    margin: 0;
    padding: 0;
    font-family: Avenir;
    font-style: normal;
    font-weight: normal;
    font-size: 16px;
    line-height: 22px;
  }

  .listing-info > *{
    margin: 0;
  }

  .listing li{
    margin-left: 30px;
  }

  .filter{
    display: flex;
    font-style: normal;
    font-family: Avenir;
    font-weight: 900;
    font-size: 12px;
    line-height: 16px;
    position: relative;
    height: 66px;
    padding: 0 15px;
    justify-content: space-between;
  }
  .listing h4{
    margin: 0;
    font-family: Avenir;
    font-style: normal;
    font-weight: 900;
    font-size: 19px;
    line-height: 27px;
    letter-spacing: 0.25px;
    color: #2E2E2E;
    margin-bottom: 5px;
  }

  .listing{
    box-sizing: border-box;
    text-align: left;
    width: 100%;
    height: 84px;
    border-radius: 10px;
    box-shadow: 0px 4px 5px rgba(0, 0, 0, 0.05), 0px 1px 10px rgba(0, 0, 0, 0.08), 0px 2px 4px rgba(0, 0, 0, 0.14);
    background-color: white;
    margin-bottom: 15px;
    padding: 15px;
  }
</style>
