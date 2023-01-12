# Ya_requests
import os
from pprint import pprint
import requests
import json

class YaUploader:
    files_url = 'https://disk.yandex.ru/client/disk/files'
    upload_link = 'https://cloud-api.yandex.net/v1/disk/resources/upload'

    def __init__(self, token: str):
        self.token = token

    @property
    def headers(self): # Определяем формат заголовков запроса
        return {
            'Content-Type': 'application/json',
            'Authorization': f"OAuth {token}"
                  }

    def get_upload_link(self, file_path):
        filename = file_path.split('/', )[-1]
        params = {'path': file_path, 'overwrite': 'true'} # Определяем параметры запроса (назначаем путь загрузки, имя файла и разрешаем перезапись)
        response = requests.get(self.upload_link, params=params, headers=self.headers) # Выполняем запрос на получение ссылки для загрузки
        # pprint(response.json())
        return response.json()

    def upload(self, file_path ):
        href = self.get_upload_link(file_path).get('href')
        if not href:
            return

        with open(file_path, 'rb') as file:
            response = requests.put(href, data=file)
            if response.status_code == 201:
                print('File uploaded successfully')
                return True
            else:
                print('File upload failed', response.status_code)

if __name__ == '__main__':
    filename = 'my_file.txt'
    token = ''
    uploader = YaUploader(token)
    result = uploader.upload(filename)
    print(result)

