# can-not-write-duplicate-eamil-and-phone-fields-Cretae-a-email-field-on-the-account-write-a-triggr-o
can not write duplicate eamil and phone fields/Cretae  a email field on the account/write a triggr on such that ..if email field and phone field already existing in the new record should not be created 
trigger avoidDuplicaterecord on Account(before insert,before update)
{
   if(trigger.isbefore && trigger.isinsert)
   {
       myAccountHandler.accountNewList(trigger.new);
   }
}








public class myAccountHandler 
{
//we have to check dupliacgate record any two fileds like email and aphone so set is better option its accept always new value
public static void accountNewList(List<account> acclist)
{
    set<string>emailset=new set<string>();
    set<string>Phoneset=new set<string>();
    
    
    for(account acc: acclist )
    {
        if(!string.isEmpty(acc.Phone)&& !string.isEmpty(acc.Email__c)) //if this two fields are not blank then add yhis value 
           {
               emailset.add(acc.Email__c);  // in email set 
               Phoneset.add(acc.Phone);  // in Phone set
               
           }
    }

  // Now we write a Quary and fetch the Phoneset and emailset records with Id
  //  
     list<account>listNewRecords=[SELECT Id,Phone,Email__c From Account Where Phone IN:Phoneset AND Email__c IN:emailset];
    
    //Now we have to use map  and take alwats email(value) in key side in map 
    //
    map<string,Account>accountMap=new map<string,Account>();
    
                        for(account acc2:listNewRecords)
                         {
                         accountMap.put(acc2.Email__c,acc2);   //we take the email on string and accut record on account side 
                          }
    //now add aall in map email fields and Phone (account),
    for(account acc3:acclist)
    {
        if(accountMap.get(acc3.Email__c)!=null &&        //old email fiels is not null
           accountMap.get(acc3.Email__c).Email__c==acc3.Email__c &&    //old email foelds is not equal to curemt email
           accountMap.get(acc3.Phone).Phone==acc3.Phone)          //ld Phone foelds is not equal to curemt Phone
            
        {
            acc3.addError('duplicate email and Phone');
        }
    }
      
}
}
