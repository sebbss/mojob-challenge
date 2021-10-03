<template>
  <div class="home">
    <job-feed
      :job-listings="jobListings"
      :position-functions="positionFunctionFilters"
      @changePag = 'reloadWithPag($event)'
      @filterFunc = 'reloadWithFilter($event)'
    />
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import JobFeed from '@/components/JobFeed.vue';
import BaseApi from '@/api-requests/api';
import { IPage, JobListing, PositionFunction } from '@/models/models';

@Component({
  components: {
    JobFeed,
  },
})
export default class Home extends Vue {
  private mojobApi: BaseApi | null = null;
  private jobListings: JobListing[] = [];
  private positionFunctionFilters: PositionFunction[] = [];
  private pageNumber: number = 5

  /**
   * Here you can do necessary request to our
   * public test-API in order to retrieve a list of job listings and a list of
   * position function filters.
   *
   * You can test the endpoints and see the documentation at:
   * https://test-api.mojob.io/public/docs/
   *
   * @private
   */
  private async mounted() {
    // Here is an example on how to retrieve job position function filters
    this.mojobApi = new BaseApi(
      'https://test-api.mojob.io/public/',
      this.axios
    );
    try {
      const jobLocationFiltersResponsePage: IPage<PositionFunction> =
        await this.mojobApi.getPositionFunctions();
      if (jobLocationFiltersResponsePage.results) {this.mojobApi = new BaseApi(
      'https://test-api.mojob.io/public/',
      this.axios
    );
        this.positionFunctionFilters = jobLocationFiltersResponsePage.results;
        console.log(JSON.stringify(this.positionFunctionFilters, null, 2));
        console.log(this.positionFunctionFilters);
      } else {
        console.log('Failed loading position function filters');
      }
    } catch (e) {
      console.log('Failed loading position function filters');
      console.log(e);
    }
    try {
      const jobListingsRes: IPage<JobListing> = await this.mojobApi.getJobListings(5);
      
      if (jobListingsRes.results) {
        this.jobListings = jobListingsRes.results;
        console.log(jobListingsRes.results);
      }
    } catch (e) {
      console.log('Failed loading jobs');
      console.log(e);
    }
  }

  async reloadWithFilter(value: number[]){
    const filterQuery:string = value.join(',')

    this.mojobApi = new BaseApi(
      'https://test-api.mojob.io/public/',
      this.axios
    );
    try {
      let jobListingsRes: IPage<JobListing>;
      if(value.length>0){
        jobListingsRes = await this.mojobApi.getJobListings(this.pageNumber, filterQuery);
      }else {
        jobListingsRes = await this.mojobApi.getJobListings(this.pageNumber);
      }
      
      
      if (jobListingsRes.results) {
        this.jobListings = jobListingsRes.results;
        console.log(jobListingsRes);
      }
    } catch (e) {
      console.log('Failed loading jobs');
      console.log(e);
    }
  }

  async reloadWithPag (value: number){
    this.pageNumber = value
    this.mojobApi = new BaseApi(
      'https://test-api.mojob.io/public/',
      this.axios
    );
    try {
      const jobListingsRes: IPage<JobListing> = await this.mojobApi.getJobListings(+value);
      
      if (jobListingsRes.results) {
        this.jobListings = jobListingsRes.results;
        console.log(jobListingsRes);
      }
    } catch (e) {
      console.log('Failed loading jobs');
      console.log(e);
    }
  }
}
</script>

<style>
  body{
    background-color: #FAFDFD;
    margin-top: 0;
  }
</style>
