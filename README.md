IEEE-CIS Fraud Detection არის ორობითი კლასიფიკაციის ამოცანა, რომლის მიზანიც არის
დაადგინოს ტრანზაქცია არის თუ არა თაღლითური. 

ამოცანის სირთულეს მგონია წარმოაგდენს მონაცემთა დიდი რაოდენობა, ფიჩერების სიმრავლე 
(394 სვეტისგან შედგება დატასეტი) და მათი არ ცოდნა, თუ რომელი ფიჩერი რას აღნიშნავს.

ჩემი მიდგომა არის ის რომ, რაღაცნაირად გამოვარკვიო საჭირო ფიჩერები, შევამცირო მათი 
რაოდენობა და დავატრენინგო ძლიერი მოდელები როგორიცაა მაგალითად XGBoost.

სულ გავტესტე სამი მოდელი: DecisionTree, RandomForest, XGBoost. 

eda.ipynb: დატის შესწავლე
model-experiment-decisiontree.ipynb: DecisionTree-ის დატრენინგება
model-experiment-randomforest.ipynb: RandomForest-ის დატრენინგება
model-experiment-xgboost_2.ipynb: XGBoost-ის დატრენინგება 
model-experiment-xgboost_1.ipynb: XGBoost-ის დატრენინგება

პირველ რიგში ტრანზაქციის და იდენტობის ბაზები გავაერთიანე TransactionID-ის მიხედვით.

ასევე რადგნაც ბაზა დიდია, დავწერე მეხსიარების შემცირების მეთოდი, რომ კეგლმა არ გამიჭედოს. 

დავამატე შემდეგი ახალი ფიჩერები:
TransactionAmt_log - ტრანზაქციის log, რამეთუ ტრანზაქციის რაოდენობა არ არის 
ბალანსირებული, არის ცოტა რაოდენობის ძალიან მაღალი გადარიცხვები

TransactionAmt_decimal - ტრანზაქციის წილადი ნაწილი

Transaction_day - გასული დღეების რაოდენობა 

Transaction_hour - რომელ საათზე შესრულდა ტრანზაქცია

Transaction_week - რომელ კვირის დღეს შესრულდა ტრანზაქცია

missing_count - სტრიქონში NaN-ების რაოდენობა

has_identity - აქვს თუ არა ტრანზაქცია იდენტობაზე ინფორმაცია

email_domain_match - ემთხვევა თუ არა გადამხდელის და მიმღების მეილის სუფიქსები

card_missing_count - ბარათის სვეტებში NaN-ების რაოდენობა

კატეგორიული ცვლადების რიცხვითებში გადასაყვანად გამოვიყენე ორი მიდგომა:
ასევე თავის დაზღვევის მიზნით უსასრულო მნიშვნელობებს ვანაცვლებ NaN-ით

Frequency Encoding და Ordinal Encoding. აქედან პირველი გამოვიყენე მაღალი 
კარდინალობის სვეტებზე, ხოლო უკანასკნელი დარჩენილებზე.

XGBoost-ს NaN მნისვნელობების დამუშავება არ სჭირდება.
DecisionTree-ს და RandomForest-ის შემთხვევაში NaN-ები ცავანაცვლე მედიანით.

შევქმენი SimpleFeatureSelector რომელიც შლის შემდეგ ფიჩერებს:
- რომლის 95% ან მეტი არის NaN.(95%-იანმა თრეშჰოლდმა დადო საუკეთესო შედეგი. გავტესტე (80, 85, 90, 95, 98))  
- რომლებიც იღებენ მხოლოდ ერთ კონრეტულ მნიშვნელობას (ესეც თავის დაზღვევის მიზნით)

RandomForest-ისა და DecisionTree-ისთვის ასევე გამოვიყენე კორელაციის ფილტრი, რომელიც
ირჩევს საუკეთესო ფიჩერებს თარგეთთან კორელაციის მიხედვით.

ვიყენებდი შემდეგ მეტრიკებს:
ROC-AUC, PR-AUC, Overfit Gap, Selected Feature Count , Dropped Feature Count.

ვალიდაციისა და ტრეინინგისთვის დატა გავყავი არა რანდომად, არამედ დროის მიხედვით, ანუ
თრენინგისთვის ავირჩიე სტრიქონების ის 80% რომლებსაც ნაკლები TransactionDT ჰქონდა.

ჰიპერპარამეტრებს ვპოულობდი ხელით და ასევე GridSearch-ის გამოყენებით.

ჰიპერპარამეტრები მოდელის მიხედვით:
    
XGBoost: n_estimators, learning_rate, max_depth, min_child_weight, subsample, colsample_bytree, gamma, reg_alpha, reg_lambda

RandomForest: n_estimators, max_depth, min_samples_split, min_samples_leaf, max_features

DecisionTree: criterion, max_depth, min_samples_split, min_samples_leaf, max_features

საუკეთესო შედეგი აჩვენა XGBoost-ის ერთ-ერთმა მოდელმა, რომელმაც ვალიდაციაზე მიიღო ROC-AUC=0.9126.

დავლოგე შემდეგი ექსპერიმენტები: XGBoost_Training, RandomForest_Training, DecisionTree_Training.

თითოეული ექსპერიმენტისთვის ვლოგავდი, feature engineering-ს, feature selection-ს, grid search-ს.

https://dagshub.com/ChorniBero15/ML2.mlflow/. 

საუკეთესო მოდელის შედეგი საბმიშენზე: 0.898339/0.931529