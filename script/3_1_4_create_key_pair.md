<!-- omit in toc -->
# キーペアの作成

## 1. 値の設定

### 1.1. リージョンの設定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. キーペア名

    KEY_PAIR='my-key'

### 1.3. キーペア保存ディレクトリ

    DIR_KEY_PAIR_DOC="${HOME}/environment/ec2-conf"

#### 1.3.1. ディレクトリの存在確認

    ls -d ${DIR_KEY_PAIR_DOC}

ディレクトリが存在しない（[ls: cannot access XXXXX : No such file or directory]と表示される場合
下記を実行しディレクトリ作成

    mkdir -p ${DIR_KEY_PAIR_DOC}

## 2. メイン処理

### 2.1. キーペアファイル名

    FILE_KEY_PAIR_DOC=${DIR_KEY_PAIR_DOC}/${KEY_PAIR}.pem \
    && echo ${FILE_KEY_PAIR_DOC}

### 2.2. キーペア作成

    aws ec2 create-key-pair \
        --key-name ${KEY_PAIR} \
        --query 'KeyMaterial' \
        --output text > ${FILE_KEY_PAIR_DOC}

## 3. 確認

### 3.1. キーペアの存在確認

キーペア名[my-key]が出力されることを確認

    aws ec2 describe-key-pairs \
        --key-name ${KEY_PAIR} \
        --query KeyPairs[].KeyName \
        --output text

### 3.2. キーペアファイルの存在確認

ファイル名が出力されることを確認

    ls ${FILE_KEY_PAIR_DOC}
