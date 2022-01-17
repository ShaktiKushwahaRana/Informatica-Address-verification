# Informatica-Address-verification




Informatica Address verification Steps:

https://docs.informatica.com/data-as-a-service/address-verification/4-0/developer-guide/introduction-to-informatica-address-verification.html

follow this documents

1) create DQC Account
there u get login id =Account Id
And ******* as password   hover on it to get disclose.

2)query vis postman
POST  https://validator5.addressdoctor.com/webservice5/v4/addressvalidation.asmx

Authentication: Basic auth
Header : Content-type text/xml   host: 127.0.0.1
Body :
<?xml version="1.0" encoding="utf-8"?>
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns="http://validator5.AddressDoctor.com/Webservice5/v4">
	<soapenv:Header/>
	<soapenv:Body>
		<Process>
			<login>167828</login>
			<password>bv!0dD3o</password>
			<parameters>
			<ProcessMode>Batch</ProcessMode> 
			</parameters>          
			<addresses>
				<Address>
					<FormattedAddress>
						<string>1575 sector 45</string> 
						<string>Chandigarh</string>
						<string></string>
					</FormattedAddress>
				</Address>
			</addresses>
		</Process>
	</soapenv:Body>
</soapenv:Envelope>
---------------------------------------------------------------------------------------------------





CALL USING LWC COMPONENT




HTML:
<template>
    
    <lightning-card title='Verify address'>

<div class="div">
        <lightning-input type="text" name="input1" label="Enter  house Number"></lightning-input>
        <lightning-input type="text"  name="input2" label="Enter Area"></lightning-input>
        <lightning-input type="text"  name="input3" label="Enter City"></lightning-input>
        <lightning-input type="text"  name="input4" label="Enter State" required="true"></lightning-input>
      <!---  <lightning-input type="Number" name="input5" label="Enter Pincode"></lightning-input>
        <lightning-input type="text"  name="input6" label="Enter landmark"></lightning-input>
        <lightning-input type="text"  name="input7" label="Enter  Country"></lightning-input> -->
    
        <div class="con">
            <lightning-button variant="success"   label="Submit" title="Successful action" onclick={handleClick} class="slds-m-left_x-small submitBtn"></lightning-button>
        </div>
        
        
        <lightning-input type="text" label="Deliverable Address" value={fResult}></lightning-input>
        <lightning-input type="text" label="Result Message" value={fmsg}></lightning-input>
    </div>
    
    </lightning-card>
</template>
________________________________________________________________________________________________

JS

import { LightningElement,api,track,wire } from 'lwc';
import apexMethodName from '@salesforce/apex/InformaticaSoapCall.callSoapApi';

export default class LwxComponent extends LightningElement {

@track input1=0;
@track input2='';
@track input3='';
@track input4='';
@track input5=0;
@track input6='';
@track input7='';
@track fResult;
@track myString='';
@track fmsg='';
@track result;

    handleClick(event)
    {
        
        console.log(event.target.label);
        var inp=this.template.querySelectorAll("lightning-input");


        inp.forEach(function(element){
            if(element.name=="input1")
              {this.input1=element.value;}  
            else if(element.name=="input2")
                {this.input2=element.value;}
                else if(element.name=="input3")
                {this.input3=element.value;}
                else if(element.name=="input4")
                {this.input4=element.value;}
                else if(element.name=="input5")
                {this.input5=element.value;}
                else if(element.name=="input6")
                {this.input6=element.value;}
                else if(element.name=="input7")
                {this.input7=element.value;}
        },this);
        this.myString=this.input1+','+this.input2+', '+this.input3+', '+this.input4+', '+this.input5+', '+this.input6+', '+this.input7;
        apexMethodName({input11:this.myString})//,input22:this.input2,input33:this.input3,input44:this.input4,input55:this.input5,input66:this.input6,input77:this.input7})
        .then(result => {
            
            if(result !=null)
            {this.fResult = result;
            this.fmsg='SUCCESS';}
            else
            {this.fResult = 'ERROR : INVALID ADDRESS';
                this.fmsg='INVALID ADDRESS';}
            
        })
        .catch(error => {
            this.error = error;
        });
        //console.log(this.input7);
    }
    
    

}
------------------------------------------------------------------------------------------------

Meta file

<?xml version="1.0" encoding="UTF-8"?>
<LightningComponentBundle xmlns="http://soap.sforce.com/2006/04/metadata">
    <apiVersion>52.0</apiVersion>
    <isExposed>true</isExposed>
    <targets>
        <target>lightning__AppPage</target>
        <target>lightning__RecordPage</target>
        <target>lightning__HomePage</target>
    </targets>
</LightningComponentBundle>





----



-----------------------------------------------------------------------------------------



cONTROLLER CLASS


global class InformaticaSoapCall {
    
    @AuraEnabled(cacheable=true)
	Webservice static String callSoapApi(String input11)
	{ 
		
        InformaticaSoap.AddressValidationSoap dem=new InformaticaSoap.AddressValidationSoap();
      	InformaticaSoap.Address  ad=new InformaticaSoap.Address();
        ad.AddressComplete=input11;
        List<InformaticaSoap.Address> addressList=new List<InformaticaSoap.Address>();
        addressList.add(ad);
        InformaticaSoap.ArrayOfAddress adds=new InformaticaSoap.ArrayOfAddress();
        adds.Address=addressList;
       	InformaticaSoap.Parameters par =new InformaticaSoap.Parameters();
        par.ProcessMode='Batch';
        InformaticaSoap.Response res=dem.Process('167828','bv!0dD3o',par,adds,null);
        if(res.StatusCode==100 && res.StatusMessage=='OK')
        {
           
           if(res.Results.result[0].ResultDataSet.ResultData !=null)
           {
               
               return res.Results.result[0].ResultDataSet.ResultData[0].Address.AddressComplete;
           } 
        }
            
        return null;
	}

}



Controller clASS
