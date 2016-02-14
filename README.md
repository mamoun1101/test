    
    let container = CKContainer.defaultContainer()
    let database = container.publicCloudDatabase
    

 //count number or records on my database
    
    let predicate = NSPredicate(value: true)
    let query = CKQuery(recordType: "Products", predicate: predicate)
    query.sortDescriptors = [NSSortDescriptor(key: "creationDate", ascending: false)]
    
    database.performQuery(query, inZoneWithID: nil, completionHandler: {
        (results, error) -> Void in
        
        if error != nil {
            
            return
        
        } else {
            
            let limit = 10
            
            let APIrequests: Int = ((Int((results?.count)!)  + limit - 1)) / limit
            
            print("\(APIrequests)") // number of requests we need
            
            print(results?.count)
            
            let downloadGroup: dispatch_group_t = dispatch_group_create()
            
            for var i = 0; i < APIrequests; i++ {}
        
        }
    })
 ///
        
    let queryOperation = CKQueryOperation(query: query)
    queryOperation.queuePriority = .VeryHigh
    queryOperation.resultsLimit = 10
    
    

    queryOperation.recordFetchedBlock = {
        record in
            self.products.append(record)
        }
        queryOperation.queryCompletionBlock = {
            cursor, error in
            if error != nil {
                let ac = UIAlertController(title: "Fetch failed", message: "There was a problem fetching the list of whistles; please try again: \(error!.localizedDescription)", preferredStyle: .Alert)
                ac.addAction(UIAlertAction(title: "OK", style: .Default, handler: nil))
                self.presentViewController(ac, animated: true, completion: nil)
                
            } else {
              
                if cursor != nil  {
                    
                    //if self.products.count == 10 {} else {}
                    
                     print("total records: \(self.products.count)")
    
                   
         
                        self.queryServer(cursor!)
                    
                    
                
                }
            }
        }
    // Clear existing  data
        self.products.removeAll(keepCapacity: true)
        database.addOperation(queryOperation)
            }
    
    
    func queryServer(cursor:CKQueryCursor)  {
        let queryOperation = CKQueryOperation(cursor: cursor)
            queryOperation.resultsLimit = 10
        
            queryOperation.recordFetchedBlock = {
                record in
                self.products.append(record)
            }
        queryOperation.queryCompletionBlock = {
                cursor, error in
                if error != nil {
                    
                    let ac = UIAlertController(title: "Fetch failed", message: "There was a problem fetching the list of whistles; please try again: \(error!.localizedDescription)", preferredStyle: .Alert)
                    ac.addAction(UIAlertAction(title: "OK", style: .Default, handler: nil))
                    self.presentViewController(ac, animated: true, completion: nil)
                    
                } else {
                    if cursor != nil {
                        print("total records: \(self.products.count)")
                        self.queryServer(cursor!)
                    } else {
                        print("completed query")
                        NSOperationQueue.mainQueue().addOperationWithBlock() {
                            
                            MBProgressHUD.hideHUDForView(self.view, animated: true)
                            
                            self.collectionView!.reloadData()
                    }
                }
            }
        }
            let container = CKContainer.defaultContainer()
            let database = container.publicCloudDatabase
            database.addOperation(queryOperation)
    }
